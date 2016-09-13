<properties
 pageTitle="Developer guide - control access to IoT Hub | Microsoft Azure"
 description="Azure IoT Hub developer guide - how to control access to IoT Hub and manage security"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# Control access to IoT Hub

## Overview

This article describes the options for securing your IoT hub. IoT Hub uses *permissions* to grant access to each IoT hub's endpoints. Permissions limit the access to an IoT hub based on functionality.

### When to use

## Access control and permissions

You can grant permissions in the following ways:

* **Hub-level shared access policies**. Shared access policies can grant any combination of the permissions listed in the previous section. You can define policies in the [Azure portal][lnk-management-portal], or programmatically by using the [Azure IoT Hub Resource provider APIs][lnk-resource-provider-apis]. A newly created IoT hub has the following default policies:

    - **iothubowner**: Policy with all permissions.
    - **service**: Policy with ServiceConnect permission.
    - **device**: Policy with DeviceConnect permission.
    - **registryRead**: Policy with RegistryRead permission.
    - **registryReadWrite**: Policy with RegistryRead and RegistryWrite permissions.


* **Per-device security credentials**. Each IoT Hub contains a [device identity registry][lnk-identity-registry]. For each device in this registry, you can configure security credentials that grant **DeviceConnect** permissions scoped to the corresponding device endpoints.

For example, in a typical IoT solution:
- The device management component uses the *registryReadWrite* policy.
- The event processor component uses the *service* policy.
- The runtime device business logic component uses the *service* policy.
- Individual devices connect using credentials stored in the IoT hub's identity registry.

For guidance on IoT Hub security topics, see the security section in [Design your solution][lnk-guidance-security].

## Authentication

Azure IoT Hub grants access to endpoints by verifying a token against the shared access policies and device identity registry security credentials.

Security credentials, such as symmetric keys, are never sent over the wire.

> [AZURE.NOTE] The Azure IoT Hub resource provider is secured through your Azure subscription, as are all providers in the [Azure Resource Manager][lnk-azure-resource-manager].

For more information about how to construct and use security tokens, see [IoT Hub security tokens][lnk-sas-tokens].

### Protocol specifics

Each supported protocol, such as MQTT, AMQP, and HTTP, transports tokens in different ways.


HTTP implements authentication by including a valid token in the **Authorization** request header.


When using [AMQP][lnk-amqp], IoT Hub supports [SASL PLAIN][lnk-sasl-plain] and [AMQP Claims-Based-Security][lnk-cbs].

If you use AMQP claims-based-security, the standard specifies how to transmit these tokens.

For SASL PLAIN, the **username** can be:

* `{policyName}@sas.root.{iothubName}` if using hub-level tokens.
* `{deviceId}` if using device-scoped tokens.

In both cases, the password field contains the token, as described in the [IoT Hub security tokens][lnk-sas-tokens] article.

When using MQTT, the CONNECT packet has the deviceId as the ClientId, {iothubhostname}/{deviceId} in the Username field, and a SAS token in the Password field. {iothubhostname} should be the full CName of the IoT hub (for example, contoso.azure-devices.net).

#### Example

Username (DeviceId is case-sensitive):
`iothubname.azure-devices.net/DeviceId`

Password (Generate SAS with Device Explorer): `SharedAccessSignature sr=iothubname.azure-devices.net%2fdevices%2fDeviceId&sig=kPszxZZZZZZZZZZZZZZZZZAhLT%2bV7o%3d&se=1487709501`

> [AZURE.NOTE] The [Azure IoT Hub SDKs][lnk-sdks] automatically generate tokens when connecting to the service. In some cases, the SDKs do not support all the protocols or all the authentication methods.

### Special considerations for SASL PLAIN

When using SASL PLAIN, a client connecting to an IoT hub can use a single token for each TCP connection. When the token expires, the TCP connection disconnects from the service and triggers a reconnect. This behavior, while not problematic for an application back-end component, is very damaging for a device-side application for the following reasons:

*  Gateways usually connect on behalf of many devices. When using SASL PLAIN, they have to create a distinct TCP connection for each device connecting to an IoT hub. This scenario considerably increases the consumption of power and networking resources, and increases the latency of each device connection.
* Resource-constrained devices are adversely affected by the increased use of resources to reconnect after each token expiration.

## Scope hub-level credentials

You can scope hub-level security policies by creating tokens with a restricted resource URI. For example, the endpoint to send device-to-cloud messages from a device is **/devices/{deviceId}/messages/events**. You can also use a hub-level shared access policy with **DeviceConnect** permissions to sign a token whose resourceURI is **/devices/{deviceId}**. This approach creates a token that is only usable to send devices on behalf of device **deviceId**.

This mechanism is similar to the [Event Hubs publisher policy][lnk-event-hubs-publisher-policy], and enables you to implement custom authentication methods.

## Custom device authentication

You can use the IoT Hub [device identity registry][lnk-identity-registry] to configure per-device security credentials and access control using [tokens][lnk-sas-tokens]. However, if an IoT solution already has a significant investment in a custom device identity registry and/or authentication scheme, you can integrate this existing infrastructure with IoT Hub by creating a *token service*. In this way, you can use other IoT features in your solution.

A token service is a custom cloud service. It uses an IoT Hub *shared access policy* with **DeviceConnect** permissions to create *device-scoped* tokens. These tokens enable a device to connect to your IoT hub.

  ![Steps of the token service pattern][img-tokenservice]

These are the main steps of the token service pattern:

1. Create an IoT Hub shared access policy with **DeviceConnect** permissions for your IoT hub. You can create this policy in the [Azure portal][lnk-management-portal] or programmatically. The token service uses this policy to sign the tokens it creates.
2. When a device needs to access your IoT hub, it requests a signed token from your token service. The device can authenticate with your custom device identity registry/authentication scheme to determine the device identity that the token service uses to create the token.
3. The token service returns a token. The token is created by using `/devices/{deviceId}` as `resourceURI`, with `deviceId` as the device being authenticated. The token service uses the shared access policy to construct the token.
4. The device uses the token directly with the IoT hub.

> [AZURE.NOTE] You can use the .NET class [SharedAccessSignatureBuilder][lnk-dotnet-sas] or the Java class [IotHubServiceSasToken][lnk-java-sas] to create a token in your token service.

The token service can set the token expiration as desired. When the token expires, the IoT hub severs the device connection. Then, the device must request a new token from the token service. If you use a short expiry time, this increases the load on both the device and the token service.

For a device to connect to your hub, you must still add it to the IoT Hub device identity registry — even though the device is using a token and not a device key to connect. Therefore, you can continue to use per-device access control by enabling or disabling device identities in the [IoT Hub identity registry][lnk-identity-registry] when the device authenticates with a token. This mitigates the risks of using tokens with long expiry times.

### Comparison with a custom gateway

The token service pattern is the recommended way to implement a custom identity registry/authentication scheme with IoT Hub. It is recommended because IoT Hub continues to handle most of the solution traffic. However, there are cases where the custom authentication scheme is so intertwined with the protocol that a service processing all the traffic (*custom gateway*) is required. An example of this is [Transport Layer Security (TLS) and pre-shared keys (PSKs)][lnk-tls-psk]. For more information, see the [protocol gateway][lnk-protocols] topic.

## Reference

### IoT Hub permissions

The following table lists the permissions you can use to control access to your IoT hub.

| Permission            | Notes |
| --------------------- | ----- |
| **RegistryRead**      | Grants read access to the device identity registry. For more information, see [Device identity registry][lnk-identity-registry]. |
| **RegistryReadWrite** | Grants read and write access to the device identity registry. For more information, see [Device identity registry][lnk-identity-registry]. |
| **ServiceConnect**    | Grants access to cloud service-facing communication and monitoring endpoints. For example, it grants permission to back-end cloud services to receive device-to-cloud messages, send cloud-to-device messages, and retrieve the corresponding delivery acknowledgments. |
| **DeviceConnect**     | Grants access to device-facing communication endpoints. For example, it grants permission to send device-to-cloud messages and receive cloud-to-device messages. This permission is used by devices. |

### Additional reference material

Other reference topics in the Developer Guide include:

- [IoT Hub endpoints][lnk-endpoints] describes the various endpoints that each IoT hub exposes for runtime and management operations.
- [Throttling and quotas][lnk-quotas] describes the quotas that apply to the IoT Hub service and the throttling behavior to expect when you use the service.
- [IoT Hub device and service SDKs][lnk-sdks] lists the various language SDKs you an use when you develop both device and service applications that interact with IoT Hub.
- [Query language for twins, methods, and jobs][lnk-query] describes the query language you can use to retrieve information from IoT Hub about your device twins, methods and jobs.
- [IoT Hub MQTT support][lnk-devguide-mqtt] provides more information about IoT Hub support for the MQTT protocol.

## Next steps

Now you have learned how to control access IoT Hub, you may be interested in the following Developer Guide topics:

- [Use device twins to synchronize state and configurations][lnk-devguide-device-twins]
- [Invoke a direct method on a device][lnk-devguide-directmethods]
- [Schedule jobs on multiple devices][lnk-devguide-jobs]

If you would like to try out some of the concepts described in this article, you may be interested in the following IoT Hub tutorials:

- [Related tutorial 1][lnk-tutorial1]
- [Related tutorial 2][lnk-tutorial2]

<!-- links and images -->

[img-tokenservice]: ./media/iot-hub-devguide-security/tokenservice.png
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-sas-tokens]: iot-hub-sas-tokens.md
[lnk-guidance-security]: iot-hub-devguide-security.md#custom-device-authentication
[lnk-amqp]: https://www.amqp.org/
[lnk-azure-resource-manager]: https://azure.microsoft.com/documentation/articles/resource-group-overview/
[lnk-cbs]: https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc
[lnk-event-hubs-publisher-policy]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab
[lnk-management-portal]: https://portal.azure.com
[lnk-sasl-plain]: http://tools.ietf.org/html/rfc4616
[lnk-identity-registry]: iot-hub-devguide-identity-registry.md
[lnk-dotnet-sas]: https://msdn.microsoft.com/library/microsoft.azure.devices.common.security.sharedaccesssignaturebuilder.aspx
[lnk-java-sas]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/com/microsoft/azure/iot/service/auth/IotHubServiceSasToken.html
[lnk-tls-psk]: https://tools.ietf.org/html/rfc4279
[lnk-protocols]: iot-hub-protocol-gateway.md

[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md