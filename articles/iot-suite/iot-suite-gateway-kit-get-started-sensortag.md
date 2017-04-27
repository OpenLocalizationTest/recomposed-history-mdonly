---
title: Connect a gateway to Azure IoT Suite using an Intel NUC | Microsoft Docs
description: Use the Microsoft IoT Commercial Gateway Kit and the remote monitoring preconfigured solution. Use the gateway to enable a SensorTag device to connect to the remote monitoring solution, send telemetry to the cloud, and respond to methods invoked from the solution dashboard.
services: ''
suite: iot-suite
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''

ms.service: iot-suite
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2017
ms.author: dobett

---
# Connect your Azure IoT gateway to the remote monitoring preconfigured solution and send telemetry from a SensorTag

[!INCLUDE [iot-suite-gateway-kit-selector](../../includes/iot-suite-gateway-kit-selector.md)]

This tutorial shows you how to use the Gateway SDK to send temperature and humidity data from SensorTag device to the cloud. The SensorTag connects to the Intel NUC gateway using Bluetooth. The tutorial uses:

- The Microsoft Azure IoT Gateway SDK to implement a sample gateway.
- The IoT Suite remote monitoring preconfigured solution as the cloud-based back end.

## Overview

In this tutorial, you complete the following steps:

- Deploy an instance of the remote monitoring preconfigured solution to your Azure subscription. This step automatically deploys and configures multiple Azure services.
- Set up your Intel NUC gateway to receive telemetry from a SensorTag device.
- Set up your Intel NUC gateway device to communicate with your computer and the remote monitoring solution.

[!INCLUDE [iot-suite-gateway-kit-prerequisites](../../includes/iot-suite-gateway-kit-prerequisites.md)]

[Texas Instruments BLE SensorTag][lnk-sensortag]. This tutorial retrieves telemetry data over Bluetooth from the SensorTag device.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> The remote monitoring solution provisions a set of Azure services in your Azure subscription. The deployment reflects a real enterprise architecture. To avoid unnecessary Azure consumption charges, delete your instance of the preconfigured solution at azureiotsuite.com when you have finished with it. If you need the preconfigured solution again, you can easily recreate it. For more information about reducing consumption while the remote monitoring solution runs, see [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config].

[!INCLUDE [iot-suite-gateway-kit-view-solution](../../includes/iot-suite-gateway-kit-view-solution.md)]

Repeat the previous steps to add a second device using a Device ID such as **device02**. The sample sends data from two simulated devices in the gateway to the remote monitoring solution.

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-connectivity](../../includes/iot-suite-gateway-kit-prepare-nuc-connectivity.md)]

## Configure Bluetooth connectivity

Configure Bluetooth on the Intel NUC to enable the SensorTag device to connect and send telemetry.

### Find the MAC address of the SensorTag

1. In the shell on the Intel NUC, run the following command to unblock the Bluetooth service:

    ```bash
    sudo rfkill unblock bluetooth
    ```

1. Run the following commands to start the Bluetooth service on the Intel NUC and enter the Bluetooth shell:

    ```bash
    sudo systemctl start bluetooth
    bluetoothctl
    ```

1. Run the following command to power on the Bluetooth controller:

    ```bash
    power on
    ```

    When the controller is on, you see a message **Changing power on succeeded**.

1. Run the following command to scan for nearby Bluetooth devices:

    ```bash
    scan on
    ```

1. Press the power button on the SensorTag to make it discoverable. The green LED flashes.

1. When you see a message in the shell that the controller has discovered the SensorTag, make a note of the MAC address of the device. The MAC address looks like **A0:E6:F8:B5:F6:00**. You need the MAC address later in the tutorial when you configure the gateway.

1. Run the following command to turn off Bluetooth scanning:

    ```bash
    scan off
    ```

1. Run the following command to verify that you can connect to the SensorTag device:

    ```bash
    connect <SensorTag MAC address>
    ```

    If connect successfully, the shell shows the message **Connection successful** and prints information about the SensorTag device. If you cannot connect, check that the SensorTag is still powered on.

1. You can now disconnect from the SensorTag and exit the Bluetooth shell by running the following commands:

    ```bash
    disconnect
    exit
    ```

[!INCLUDE [iot-suite-gateway-kit-prepare-nuc-software](../../includes/iot-suite-gateway-kit-prepare-nuc-software.md)]

## Configure and run the sample

You can now configure the gateway software on your Intel NUC to communicate with the remote monitoring solution.

> [!NOTE]
> In this tutorial, you use the standard **vi** text editor on the Intel NUC. If you have not used **vi** before, you should complete an introductory tutorial such as [Unix - The vi Editor Tutorial][lnk-vi-tutorial] to familiarize yourself with the editor.

Open the sample configuration file in the **vi** editor using the following command:

`vi /tmp/azure-remote-monitoring-gateway-intelnuc/samples/ble_gateway/src/gateway_sample.json`

Locate the following lines in the configuration for the IoTHub module:

```json
"args": {
  "IoTHubName": "<<insert here IoTHubName>>",
  "IoTHubSuffix": "<<insert here IoTHubSuffix>>",
  "Transport": "amqp"
}
```

Replace the placeholder values with the IoT Hub information you created and saved at the start of this tutorial. The value for IoTHubName looks like **yourrmsolution37e08**, and the value for IoTSuffix is typically **azure-devices.net**.

Locate the following lines in the configuration for the mapping module:

```json
args": [
  {
    "macAddress": "AA:BB:CC:DD:EE:FF",
    "deviceId": "<<insert here deviceId>>",
    "deviceKey": "<<insert here deviceKey>>"
  }
]
```

Replace the **deviceID** and **deviceKey** placeholders with the IDs and keys for the two devices you created in the remote monitoring solution previously. Replace the MAC address with the MAC address you noted previously of your SensorTag device.

Locate the following lines in the configuration for the SensorTag module:

```json
"args": {
  "controller_index": 0,
  "device_mac_address": "<<AA:BB:CC:DD:EE:FF>>",
  "instructions": [
```

Replace the device MAC address with the MAC address you noted previously of your SensorTag device.


Locate the following lines in the configuration for the Logger module:

```json
"args": {
  "filename": "<</path/to/log-file.log>>"
}
```

Set the filename to **/tmp/gateway.log**.

Save your changes to the configuration file.

You can now run the gateway using the following command:

```bash
cd /tmp/azure-remote-monitoring-gateway-intelnuc/build
sudo ./samples/ble_gateway/ble_gateway ../samples/ble_gateway/src/gateway_sample.json
```

The gateway starts on the Intel NUC and forwards telemetry from the SensorTag device to the remote monitoring solution:

![Gateway forwards SensorTag telemetry][img-simulated telemetry]

Press **Ctrl-C** to exit the program at any time.

## View the telemetry

The gateway is now sending telemetry from the SensorTag device to the remote monitoring solution. You can view the telemetry on the solution dashboard. You can also send commands to your SensorTag device through the gateway from the solution dashboard.

- Navigate to the solution dashboard.
- Select the device you configured in the gateway that represents the SensorTag in the **Device to View** dropdown.
- The telemetry from the SensorTag device displays on the dashboard.

![Display telemetry from the SensorTag devices][img-telemetry-display]

## Call a command

TODOTODO - Fix this section

From the solution dashboard, you can invoke methods on your Raspberry Pi. When the Raspberry Pi connects to the remote monitoring solution, it sends information about the methods it supports.

- In the solution dashboard, click **Devices** to visit the **Devices** page. Select your Raspberry Pi in the **Device List**. Then choose **Methods**:

    ![List devices in dashboard][img-list-devices]

- On the **Invoke Method** page, choose **LightBlink** in the **Method** dropdown.

- Choose **InvokeMethod**. The simulator prints a message in the console on the Raspberry Pi. The app on the Raspberry Pi sends an acknowledgment back to the solution dashboard:

    ![Show method history][img-method-history]

- You can switch the LED on and off using the **ChangeLightStatus** method with a **LightStatusValue** set to **1** for on or **0** for off.

> [!WARNING]
> If you leave the remote monitoring solution running in your Azure account, you are billed for the time it runs. For more information about reducing consumption while the remote monitoring solution runs, see [Configuring Azure IoT Suite preconfigured solutions for demo purposes][lnk-demo-config]. Delete the preconfigured solution from your Azure account when you have finished using it.


## Next steps

Visit the [Azure IoT Dev Center](https://azure.microsoft.com/develop/iot/) for more samples and documentation on Azure IoT.

[img-simulated telemetry]: ./media/iot-suite-gateway-kit-get-started-sensortag/appoutput.png


[img-telemetry-display]: ./media/iot-suite-gateway-kit-get-started-sensortag/telemetry.png

[img-list-devices]: media/iot-suite-raspberry-pi-kit-view-telemetry-sensortag/listdevices.png
[img-method-history]: media/iot-suite-raspberry-pi-kit-view-telemetry-sensortag/methodhistory.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md
[lnk-vi-tutorial]: http://www.tutorialspoint.com/unix/unix-vi-editor.htm
[lnk-sensortag]: http://www.ti.com/ww/en/wireless_connectivity/sensortag/