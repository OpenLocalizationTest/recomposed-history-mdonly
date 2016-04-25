<properties
	pageTitle="Simulate a device with the Gateway SDK | Microsoft Azure"
	description="Azure IoT Hub Gateway SDK walkthrough using Linux to illustrate sending telemetry from a simulated device using the Azure IoT Hub Gateway SDK."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# IoT Gateway SDK – send device-to-cloud messages with a simulated device using Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Build and run the sample

Before you get started, you must:

- [Set up your development environment][lnk-setupdevbox] for working with the SDK on Linux.
- [Create an IoT hub][lnk-create-hub] in your Azure subscription, you will need the name of your hub to complete this walkthrough. If you don't already have an Azure subscription, you can get a [free account][lnk-free-trial].
- Add two devices to your IoT hub and make a note of their ids and device keys. You can use the [Device Explorer or iothub-explorer][lnk-explorer-tools] tool to add your devices to the IoT hub you created in the previous step and retrieve their keys.

To build the sample:

1. Open a shell.
2. Navigate to the **azure-iot-gateway-sdk/tools/** folder in your local copy of the repository.
3. Run the **build.sh** script.

To run the sample:

In a text editor, open the file **azure-iot-gateway-sdk/samples/simple_device_cloud_upload/src/simple_device_cloud_upload_lin.json** in your local copy of the repository. This file configures the modules in the sample gateway:

- The **IoTHub** module connects to your IoT hub. You must configure it to send data to your IoT hub. Specifically, set the **IoTHubName** value to the name of your IoT hub and set the value of **IoTHubSuffix** to **azure-devices.net**.
- The **mapping** module maps the MAC addresses of your simulated devices to your IoT Hub device ids. Make sure that **deviceId** values match the ids of the two devices you added to your IoT hub, and that the **deviceKey** values contain the keys of your two devices.
- The **BLE1** and **BLE2** modules are the simulated devices. Note how their MAC addresses match those in the **mapping** module.
- The **Logger** module logs your gateway activity to a file.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./modules/iothub_http/libiothub_http_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./modules/mac_iothub_id_map/libmac_iothub_id_map_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}

```

Save any changes you made to the configuration file.

To run the sample:

1. In your shell, navigate to the **build** folder in your local copy of the repository.
2. Run the following command:

    ```
    ./samples/device_cloud_upload/device_cloud_upload {Path to your local copy of the repository}/azure-iot-field-gateway-sdk/samples/device_cloud_upload/src/device_cloud_upload_lin.json
    ```

3. You can use the [Device Explorer or iothub-explorer][lnk-explorer-tools] tool monitor the messages that IoT hub receives from the gateway.

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-field-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md