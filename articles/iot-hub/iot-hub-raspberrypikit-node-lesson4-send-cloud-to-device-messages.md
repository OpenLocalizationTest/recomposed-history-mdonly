<properties
 pageTitle="Send messages from IoT Hub to your device | Microsoft Azure"
 description="placeholder"
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang=""
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date=""
 ms.author=""/>

# 4.1 Get your hands on the sample application

The sample application runs on your Pi and monitors incoming messages from your IoT hub. It also sends messages to your Pi from your IoT hub to blink the LED.

## 4.1.1 What will you do

- Connect the sample application to your IoT hub
- Deploy and run the sample application
- Send messages from your IoT hub to your Pi to blink the LED

## 4.1.2 What do you need

- A Raspberry Pi 3 that is set up for use. To learn how to set up your Pi, see [Lesson 1: Get started with your Raspberry Pi 3 device](http://www.microsoft.com)
- An IoT hub that is created in your Azure subscription. To learn how to create your Azure IoT Hub, see [Lesson 2: Create your Azure IoT Hub](http://www.microsoft.com)

## 4.1.3 Connect the sample application to your IoT hub

1. Open the sample application in Visual Studio Code by running the following commands

    ```bash
    cd Lesson4
    code .
    ```

    Notice the app.js file in the app sub-folder. The app.js file is the key source file that enables the application to monitor incoming messages from IoT Hub. The blinkLED function blinks the LED.

2. Initialize the configuration file with the following commands.

    ```bash
    npm install
    gulp init
    ```

    If you've completed Lesson3 in this machine, you might skip to next section. Or you might need the following commands to replace the placeholders in the `config-raspberrypi.json` in your home folder.

- Replace **[device hostname or IP address]** with your Pi’s IP address or hostname that you got from `devdisco list --eth`
- Replace **[IoT device connection string]** with the device connection string from the command `az iot hub show-connection-string --name {my hub name} --resource-group {resource group name}`.
- Replace **[IoT hub connection string]** with the IoT hub connection string from the command `az iot device show-connection-string --hub {my hub name} --device-id {device id} --resource-group {resource group name}`.


## 4.1.5 Deploy and run the sample application

Deploy and run the sample application on your Pi by running the following commands:
  
```
npm install
gulp
```

The gulp command sequentially runs the install-tools, the deploy, and the run tasks.

Once the sample application runs, it stands by for incoming messages from your IoT hub. Meanwhile, the sample application sends a bunch of “blink” messages from your IoT Hub to your Pi. For each of the blink message received, the sample application calls the blinkLED function to blink the LED.

## 4.1.6 Send messages from your IoT hub to your Pi to blink the LED

You should see the LED blinking every two seconds as the gulp task is sending 20 messages from your IoT hub to your Pi. The last one is a “stop” message which tells the application to stop running.

Congratulation! You’ve successfully achieved the task that the lesson is designed for.

## 4.1.7 Summary

You’ve learned how to send messages from your IoT hub to your Pi to blink the LED. We provide complimentary reading to show you how to change the on and off behavior of the LED.
