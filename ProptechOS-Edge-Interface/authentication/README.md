![ProptechOS logo](../../images/ProptechOS-logotype-ex.png)
# Edge Authentication
Azure IoT Hub uses **Shared Access Signature (SAS) tokens** to authenticate devices.
There are several ways how you can generate those tokens(will be described a bit later). 
Regardless of which way you choose, it requires three things from you:
1. IoT Hub Hostname (iotHubHostname in the examples)
2. Device ID (deviceId in examples)
3. Device Key (deviceKey in examples)

All these things you can retrieve from the REST API.

### 1. IoT Hub Hostname
First authorize ProptechOS REST API, see [ProptechOS-Api](../ProptechOS-Api) for directions on how to do that.
Now you can get IoT Hub Hostname simply by querying for your property owner, using the 
```curl
GET foo.proptechos.com/api/propertyowner/default
```
or
```curl
GET foo.proptechos.com/api/propertyowner/{id}
```
in response body you will see Property Owner object with property *iotHubHostname* which is the actual hostname
of the iot hub you need to connect to.

### Device ID
To retrieve device id from ProptechOS REST API, please use
```curl
GET foo.proptechos.com/api/device
```
this endpoint will help you to find device you are looking for. Don`t forget that twins with *"class": "Device"* 
are the only one you are interested in. Sensors and Actuators do not have any representations in iot hub.
### Device Key
Now when we have iot hub hostname and device id, the last thing we need is device key.
To retrieve device key you will need device id that you received in the previous step.
Use this endpoint to get primary iot hub device key:
```curl
GET foo.proptechos.com/api/device/{id}/key
```

## Using Azure SDKs
The easies and probalby the fastest way to connect to device and send device to cloud(D2C) and receieve cloud to device messages(C2D) 
is to use [Azure IoT SDKs](https://learn.microsoft.com/en-us/azure/iot-hub/iot-hub-devguide-sdks) 
it automatically generates sas tokens without requiring any special configuration. All you need is a 
**Connection string** that has next structure:
```
HostName={iotHubHostname}.azure-devices.net;DeviceId={deviceId};SharedAccessKey={deviceKey}
```

## Generation of SAS token
In case you want to use [MQTT](https://learn.microsoft.com/en-us/azure/iot-hub/iot-hub-mqtt-support), 
[AMQP](https://learn.microsoft.com/en-us/azure/iot-hub/iot-hub-amqp-support) or HTTPS protocols directly, you will need to generate sas token by yourself.
There is a very good instruction in microsoft documentation about [how to generate SAS token](https://learn.microsoft.com/en-us/azure/iot-hub/iot-hub-dev-guide-sas?tabs=java#sas-token-structure).
And there are code examples for variety of programming languages like **Phyton, Java, Node.js, C#**.
SAS token is used as the **Password** field. The format of the SAS token is the same as for MQTT, HTTPS and AMQP protocols

!!! Keep in mind that your sas token refers to device-registry credentials. So *{policyName}* can be ignored and not specified, when you generate token.

