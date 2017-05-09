---
title: Create a CI/CD pipeline in Azure with VSTS | Microsoft Docs
description: Learn how to create a VSTS pipeline for continuous integration and delivery that deploys a web app to IIS on a Windows VM
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/09/2017
ms.author: iainfou
---

# Create a continuous integration pipeline with VSTS and IIS on a Windows virtual machine in Azure

## Create project in VSTS
To manage the code commit process, build definitions, and release definitions, create a project in VSTS as follows:

1. Open your VSTS dashboard in a web browser and click **New project**.
2. Enter *myWebApp* for the **Project name**. Leave all other default values to use *Git* version control and *Agile* work item process.
3. Select the option to **Share with** *Team Members* and then click **Create**.
5. One your project has been created, select the option to **Initialize with a README or gitignore** and then click **Initialize**.
6. Inside your new project, select **Dashboards** across the top and then click **Open in Visual Studio**.


## Create ASP.NET web application
In the previous step, you created a project in VSTS. The final step opens your new project in Visual Studio. You manage your code and commits in the **Team Explorer** window. Create a local copy of your new project, then create an ASP.NET web application from a template as follows:

1. Click **Clone** to create a local git repo of your VSTS project.
    
    ![Clone repo from VSTS project](media/tutorial-vsts-iis-cicd/clone_repo.png)

2. Under **Solutions**, click **New**.

    ![Create web application solution](media/tutorial-vsts-iis-cicd/new_solution.png)

3. Select **Web** templates, and then choose the **ASP.NET Web Application** template.
    a. Enter a name for your application, such as *myWebApp*, and uncheck the box for **Create directory for solution**.
    b. If the option is available, uncheck the box to **Add Application Insights to project**. Application Insights requires you to authorize your web application with Azure Application Insights. To keep it simple in this tutorial, skip this process.
    c. Click **OK**.
4. Select **MVC** from the template list.
    a. Click **Change Authentication**, select **No Authentication**, and then click **OK**.
    b. Click **OK** to create your solution.
5. In the **Team Explorer** window, click **Changes**.

    ![Commit local changes to VSTS git repo](media/tutorial-vsts-iis-cicd/commit_changes.png)

6. In the commit text box, enter *Initial commit*. Select **Commit All and Sync** from the drop-down menu.


## Create build definition
1. In VSTS, you should be on the *myWebApp* project landing page.
2. To create a build definition, click **Build & Release** across the top, then select **Builds**.
3. Click **+ New definition**.
4. Select the **ASP.NET (PREVIEW)** template and click **Apply**.
5. You can leave all the default tasks. Under **Get sources**, ensure the *myWebApp* repository and *master* branch are selected.

    ![Create build definition in VSTS project](media/tutorial-vsts-iis-cicd/create_build_definition.png)

6. On the **Triggers** tab, click the slider for **Enable this trigger** to *Enabled*.
7. Save the build definition and queue a new build by selecting the **Save & queue** , then **Save & queue** again. Leave the defaults and click **Queue**.

Watch as the build is scheduled on a hosted agent, then begins to build.

![Successful build of VSTS project](media/tutorial-vsts-iis-cicd/successful_build.png)


## Create VM
Create a Windows Server 2016 VM using [this script sample](../scripts/virtual-machines-windows-powershell-sample-create-vm.md?toc=%2fpowershell%2fmodule%2ftoc.json). It takes a few minutes for the script to run and create the VM. Once the VM has been created, open port 80 for web traffic:

```powershell
Get-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $resourceGroup `
  -Name "myNetworkSecurityGroup" | `
Add-AzureRmNetworkSecurityRuleConfig `
  -Name "myNetworkSecurityGroupRuleWeb" `
  -Protocol "Tcp" `
  -Direction "Inbound" `
  -Priority "1001" `
  -SourceAddressPrefix "*" `
  -SourcePortRange "*" `
  -DestinationAddressPrefix "*" `
  -DestinationPortRange "80" `
  -Access "Allow" | `
Set-AzureRmNetworkSecurityGroup
```

Obtain the public IP address of your VM:

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup | Select IpAddress
```

Create a remote desktop session to your VM:

```cmd
mstsc /v:<publicIpAddress>
```

Open an **Administrator PowerShell** command prompt. Install IIS and required .NET features as follows:

```powershell
Install-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features
```


## Create VSTS deployment group
1. In VSTS, click **Build & Release** and then **Deployment groups**.
2. Click **Add Deployment group**.
3. Enter a name for the group, such as *myIIS*, and click **Create**.
4. In the **Register machines** section, ensure *Windows* is selected, then click the checkbox to **Use a personal access token in the script for authentication**.
5. Click **Copy script to clipboard**.


### Add IIS VM to the deployment group
1. Back in your **Administrator PowerShell** session on your VM, paste and run the script copied from VSTS.
2. When prompted to configure tags for the agent, press *Y* and enter *web*.
3. When prompted for the user account, press *Return* to accept the defaults.
4. Wait for the script to finish with a message `Service vstsagent.account.computername started successfully`.
5. In the **Deployment groups** page of the **Build & Release** menu, open the *myIIS* deployment group. On the **Machines** tab, verify that your VM is listed.

    ![VM successfully added to VSTS deployment group](media/tutorial-vsts-iis-cicd/deployment_group.png)


## Create release definition

1. Click **Build & Release**, then select **Builds**. Select the build definition created in a previous step.
2. Under **Recently completed**, click the most recent build and then click **Release**.
3. Click **Yes** to create a release definition.
4. Select the **Empty** template and click **Next**.
5. Verify the project and source build definition are populated with your project.
6. Select the **Continuous deployment** check box, and then choose **Create**.
7. Click the drop-down box next to **+ Add tasks** and select *Add a deployment group phase*.
    
    ![Add task to release definition in VSTS](media/tutorial-vsts-iis-cicd/add_release_task.png)
8. Click **Add** next to **IIS Web App Deploy(Preview)**, then click **Close**.
9. Click the **Run on deployment group** parent task.
    a. For **Deployment Group**, select the deployment group you created earlier, such as *myIIS*.
    b. In the **Machine tags** box, click **Add** and select the *web* tag.
    
    ![Release definition deployment group task for IIS](media/tutorial-vsts-iis-cicd/release_definition_iis.png)
 
11. Click the **Deploy: IIS Web App Deploy** task to configure your IIS instance settings as follows:
    e. For **Website Name**, enter *Default Web Site*. If you created a different website on the IIS servers, use that name instead.
    f. Leave all the other default settings.
12. Click **Save**, then click **OK** twice.


## Create release and publish
1. In your release definition, click **+ Release**, then click **Create Release**.
2. Verify that the latest build is selected in the drop-down list, along with **Automated deployment: After release creation**. Click **Create**.
3. A small banner appears across the top of your release definition, such as *Release 'Release-1' has been created*. Click the release link.
4. Open the **Logs** tab to watch the release progress.
    
    ![Successful VSTS release and web deploy package push](media/tutorial-vsts-iis-cicd/successful_release.png)

5. After the release is complete, open a web browser and enter the public IIP address of your VM. Your ASP.NET website is running.

    ![ASP.NET web app running on IIS VM](media/tutorial-vsts-iis-cicd/running_web_app.png)


## Test the whole CI/CD pipeline
1. In Visual Studio, open the **Solution Explorer** window.
2. Navigate to and open *myWebApp | Views | Home | Index.cshtml*
3. Edit line 6 (`<h1>ASP.NET</h1>`) to read:

    `<h1>ASP.NET with VSTS and CI/CD!</h1>`

4. Save the file.
5. Open the **Team Explorer** window, select the *myWebApp* project, then click **Changes**.
6. Enter a commit message, such as *Testing CI/CD pipeline*, then select **Commit All and Sync** from the drop-down menu.
7. In VSTS workspace, a new build is triggered from the code commit. Click **Build & Release**, then **Builds**. Select your build definition, then click the **Queued & running** build to watch as the build progresses.
8. Once the build is successful, a new release is triggered. Click **Build & Release**, then **Releases** to see the web deploy package pushed to your IIS VM. Click the **Refresh** icon to update the status. When the *Environments* column shows a green check mark, the release has successfully deployed to IIS.
9. To see your changes applied, refresh your IIS website in a browser.

    ![ASP.NET web app running on IIS VM from CI/CD pipeline](media/tutorial-vsts-iis-cicd/running_web_app_cicd.png)


## Next steps