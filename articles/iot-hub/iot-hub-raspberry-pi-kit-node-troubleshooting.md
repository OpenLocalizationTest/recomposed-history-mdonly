<properties
 pageTitle="Troubleshooting | Microsoft Azure"
 description="Troubleshooting page for Raspberry Pi Node.js experience"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/28/2016" 
 ms.author="xshi"/>

# Troubleshooting

## Hardware issues
### The application runs well but the LED is not blinking
This issue is always related to the hardware circuit connectivity. Follow the steps below to quickly identify problems.

1. Check if you choose the correct **GPIO** on your board. The two ports in this lesson should be **GPIO GND (Pin 6)** and **GPIO 04 (Pin 7)**.
2. Check if the polarity of your LED is correct. The longer leg should indicate the **positive**, anode pin.
3. Use the **3.3V Pin** and **GND Pin** on your Raspberry Pi 3. Treat your Pi as the DC Power. Check if the LED works fine.

![LED spec](media/iot-hub-raspberry-pi-lessons/troubleshooting/led_spec.png)

### Other hardware issues
For the most common problems on Raspberry Pi 3, you can refer to the [official troubleshooting page](http://elinux.org/R-Pi_Troubleshooting) to seek solutions.

## Node.js package issues
### No response during gulp tasks
If you got some problems during gulp tasks, you can add `--verbose` option for debugging. Try to terminate current gulp taks with `Ctrl + C` and run the below command in your console window to see debug messages. You might see detailed error messages printed in your console output. 

```bash
gulp --verbose
```

### Device discovery issues
For help troubleshooting common problems in `devdisco`, please check the [readme](https://github.com/Azure/device-discovery-cli/blob/develop/readme.md).

### Other NPM issues
Try to update your NPM package with the following command, the 

```bash
npm install -g npm
```

If the problem still exists, leave your comments below or create a Github issue in our [Sample Repository](https://github.com/Azure-Samples/iot-hub-node-raspberrypi-getting-started)

## Remote debugging

### Run the sample application in debug mode

```bash
gulp run --debug
```

Once the debug engine is ready, you should be able to see ```Debugger listening on port 5858``` from the console output.

### Configure VS Code to connect to the remote device

Open the **Debug** panel from the left side.

Click the green **Start Debugging** (F5) button. VS Code would open a **launch.json** file, which you need to update.

Update the **launch.json** file with the following content, replace `[device hostname or IP address]` with the actual device IP address or hostname.   

```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "Attach",
            "type": "node",
            "request": "attach",
            "port": 5858,
            "address": "[device hostname or IP address]",
            "restart": false,
            "sourceMaps": false,
            "outDir": null,
            "localRoot": "${workspaceRoot}",
            "remoteRoot": null
        }
    ]
}
```

![Remote debugging configuration](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_configuration.png)

### Attach to the remote application

Click the green **Start Debugging** (F5) button and enjoy debugging. 

You can read [JavaScript in VS Code](https://code.visualstudio.com/docs/languages/javascript#_debugging) to learn more about the debugger.

![Remote debugging interactive](media/iot-hub-raspberry-pi-lessons/troubleshooting/remote_debugging_interactive.png)

## Azure-cli issues
Azure-cli is a preview build, you can refer to [Preview Install Guide](https://github.com/Azure/azure-cli/blob/master/doc/preview_install_guide.md) to seek solutions.

If you encounter any bugs with the tool please file an [issue](https://github.com/Azure/azure-cli/issues) in the Issues section of our GitHub repo.

For help troubleshooting common problems, please check the [readme](https://github.com/Azure/azure-cli/blob/master/README.rst).

## Python installation issues

### Lagacy installation issues (MacOS)
When installing pip, permission error will be thrown when there are legacy packages, which are installed with su permission. This is because previous install of Python via brew(MacOS) is not uninstalled completely.Some pip packages from previous installation is created by root, which caused permission error. Solution is to remove those packages installed by root. Follow below instructions:

1. Go to /usr/local/lib/python2.7/site-packages
2. List packages create by root: `ls -l | grep root`
3. Uninstall packages from step2: `sudo rm -rf {package name}`
4. Reinstall Python.

## Azure IoT hub issues

If you've successfully provisioned Azure IoT hub with `azure-cli`, but you also need a tool to manage devices connecting to your IoT hub. You can try the following tools.

### Device Explorer

[Device Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/doc/how_to_use_device_explorer.md) runs on your Windows local machine and connects to your IoT hub in Azure. It communicates with the following [IoT Hub endpoints](iot-hub-devguide.md).

- *Device identity management* to provision and manage devices registtered with your IoT hub.
- *Receive device-to-cloud* to enable you to monitor messages sent from your device to your IoT hub.
- *Send cloud-to-device* to enable you to send messages to your devices from your IoT hub.

You need to configure your `IoT hub connection string` within this tool to use all the capabilities above.

### IoT hub Explorer

[IoT hub Explorer](https://github.com/Azure/azure-iot-sdks/blob/master/tools/iothub-explorer/readme.md) is a sample multiplatform CLI tool to manage device clients using the Azure IoT Hub service SDK. The tool enables you to manage the devices in the identity register, monitor device-to-cloud messages, and send cloud-to-device commands.

To install the latest (pre-release) version of the iothub-explorer tool, run the following command in your command-line environment:

```
npm install -g iothub-explorer@latest
```

You can use the following command to get additional help about all the iothub-explorer commands and their parameters:

```bash
iothub-explorer help
```

### Use Azure Portal to manage your resouces

In all these lessons, a full CLI experience is provided to create and manage all your Azure resouces. You might also want use the [Microsoft Azure portal](../azure-portal-overview.md) to help provision, manage and debug your Azure resources.

## Azure storage issues

[Microsoft Azure Storage Explorer (Preview)](http://storageexplorer.com) is a standalone app from Microsoft that allows you to easily work with Azure Storage data on Windows, macOS and Linux. This tool allows you to connect to you table and see the data in it. You can use this tool to troubleshoot your Azure storage issues.
