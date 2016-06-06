<properties
   pageTitle="Debugging apps in a local Docker container | Microsoft Azure"
   description="Learn how to modify an app that is running in a local Docker container, refresh the container via Edit and Refresh and set debugging breakpoints"
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/06/2016"
   ms.author="allclark" />

# Debugging apps in a local Docker container

## Overview
The Visual Studio Tools for Docker provides a consistent way to develop and validate your application
locally in a Linux Docker container without having to restart the container each time you make a code change.
This article will illustrate how to use the "Edit and Refresh" feature to start an ASP.NET Core Web app in a local Docker container,
make any necessary changes, and then refresh the browser to see those changes, as well as set breakpoints for debugging. 

> [AZURE.NOTE] Windows Container support will be coming in a future release

## Prerequisites
The following tools need to be installed.

- [Visual Studio 2015 Update 2](https://go.microsoft.com/fwlink/?LinkId=691978)
- [Microsoft ASP .NET Core RC 2](http://go.microsoft.com/fwlink/?LinkId=798481)
- [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS)

To run Docker containers locally, you'll need a local docker client.
You can use the released [Docker Toolbox](https://www.docker.com/products/overview#/docker_toolbox) which requires Hyper-V to be disabled,
or you can use [Docker for Windows Beta](https://beta.docker.com) which uses Hyper-V, and requires Windows 10.

If using Docker Toolbox, you'll need to [Configure the Docker client](./vs-azure-tools-docker-setup.md).

## Editing an app running in a local Docker container
Visual Studio 2015 Tools for Docker enables ASP .NET Core RC2 Web app developers to test and run their application in a Docker container,
make changes to the application in Visual Studio and refresh the browser to see changes applied to the app running inside of the container.
With .NET Core and Visual Studio Tools for Docker version 0.20, you can also set breakpoints for the code running with the Docker Container.

1. From the Visual Studio menu, select **File > New > Project**. 

1. Under the **Templates** section of the **New Project** dialog box, select **Visual C# > Web**.

1. Select **ASP.NET Core Web Application (.NET Core)**.

1. Give your new application a name (or take the default) and Tap **OK**.  

1. Under **ASP.NET Core Templates**, select **Web Application** and Tap **OK**.

1. Deselect **Host in the cloud** as you'll be using Docker as your deployment solution.

1. From the Visual Studio Solution Explorer, right-click the project and select **Add > Docker Support**.

	![][0]
 
1. The following files are created under the project node:

	![][1]

> [AZURE.NOTE] If using the [Docker for Windows Beta](https://beta.docker.com), open Properties\Docker.props and remove the default value and restart Visaul Studio for the value to take affect.
	![][2]

##Edit & Refresh
To quickly iterate changes, you can start your application within a container, and continue to make changes, viewing them as you would with IIS Express.
 
1. Set the Solution Configuration to `Debug` and press **&lt;CTRL + F5>** to build your docker image and run it locally. See the output window, using build or 

1. Once the container image has been built and is running in a Docker container, Visual Studio will try to launch the Web app in your default browser. If you are using the Microsoft Edge browser or otherwise have errors, see the [Troubleshooting](#troubleshooting) section.

1. Return to Visual Studio and open `Views\Home\About.cshtml`. 

1. Append the following HTML content to the end of the file and save the changes. 

    ```html
    <h1>Hello from a Docker Container!</h1>
    ```
    
1.	Viewing the output window, when the .NET build is completed and you see `Application started. Press Ctrl+C to shut down`, switch back to your browser and refresh the page.

1.	You should see changes have been applied!

##Breakpoint Debugging
Often, changes will need further inspection, leveraging the debugging features of Visual Studio.

1.	Return to Visual Studio and open `Controllers\HomeController.cs`

1.  Replace the contents of the About() method with the following:

    ```
    string message = "Your application description page from wthin a Container";
    ViewData["Message"] = message;
    ```

1.  Set a breakpoint to the left of the `string message`... line.

1.  Hit **&lt;F5>** to start debugging.

1.  Navigate to the About page to hit your breakpoint.

1.  Switch to Visual Studio to view the breakpoint, and inspect the value of message.

	![][3]

##Sumamry
With [Visual Studio 2015 Tools for Docker](https://aka.ms/DockerToolsForVS), you can get the productivity of working locally,
with the production realism of developing within a Docker container. 

##TODO:

*links to details on the files added to the project*
*link to the troubleshooting doc - resolve the duplicate docs
[3]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png