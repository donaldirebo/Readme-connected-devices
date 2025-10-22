# Lab Module 06 - MQTT Client (CDA)

## Overview

Lab Module 06 implements MQTT (Message Queuing Telemetry Transport) client functionality for the Constrained Device Application (CDA). This enables the CDA to communicate with an MQTT broker for publishing sensor/performance data and subscribing to actuator commands.

## Objectives

- Implement MqttClientConnector using the paho-mqtt library
- Handle MQTT connection, disconnection, and network events
- Implement publish and subscribe functionality with QoS validation
- Integrate MQTT client into DeviceDataManager
- Enable pub/sub messaging between CDA and GDA via MQTT broker

## Code Repository and Branch
https://github.com/donald4u/cda-python-components/tree/labmodule06

## Class Diagram
```mermaid
classDiagram
    class IPubSubClient {
        <<interface>>
        +connectClient() bool
        +disconnectClient() bool
        +publishMessage(resource, msg, qos) bool
        +subscribeToTopic(resource, callback, qos) bool
        +unsubscribeFromTopic(resource) bool
        +setDataMessageListener(listener) bool
    }

    class IDataMessageListener {
        <<interface>>
        +handleActuatorCommandMessage(data) ActuatorData
        +handleActuatorCommandResponse(data) bool
        +handleIncomingMessage(resource, msg) bool
        +handleSensorMessage(data) bool
        +handleSystemPerformanceMessage(data) bool
    }

    class MqttClientConnector {
        -config: ConfigUtil
        -dataMsgListener: IDataMessageListener
        -host: str
        -port: int
        -keepAlive: int
        -defaultQos: int
        -mc: paho.mqtt.Client
        +__init__(clientID)
        +connectClient() bool
        +disconnectClient() bool
        +onConnect(client, userdata, flags, rc)
        +onDisconnect(client, userdata, rc)
        +onMessage(client, userdata, msg)
        +onPublish(client, userdata, mid)
        +onSubscribe(client, userdata, mid, granted_qos)
        +onActuatorCommandMessage(client, userdata, msg)
        +publishMessage(resource, msg, qos) bool
        +subscribeToTopic(resource, callback, qos) bool
        +unsubscribeFromTopic(resource) bool
        +setDataMessageListener(listener) bool
    }

    class DeviceDataManager {
        -configUtil: ConfigUtil
        -enableMqttClient: bool
        -enableSystemPerf: bool
        -enableSensing: bool
        -enableActuation: bool
        -mqttClient: MqttClientConnector
        -sysPerfMgr: SystemPerformanceManager
        -sensorAdapterMgr: SensorAdapterManager
        -actuatorAdapterMgr: ActuatorAdapterManager
        -actuatorResponseCache: dict
        -sensorDataCache: dict
        -systemPerformanceDataCache: dict
        +__init__()
        +startManager()
        +stopManager()
        +getLatestActuatorDataResponseFromCache(name) ActuatorData
        +getLatestSensorDataFromCache(name) SensorData
        +getLatestSystemPerformanceDataFromCache(name) SystemPerformanceData
        +handleActuatorCommandMessage(data) ActuatorData
        +handleActuatorCommandResponse(data) bool
        +handleIncomingMessage(resource, msg) bool
        +handleSensorMessage(data) bool
        +handleSystemPerformanceMessage(data) bool
        -_handleSensorDataAnalysis(data)
        -_handleUpstreamTransmission(resourceName, msg)
    }

    class ResourceNameEnum {
        <<enumeration>>
        CDA_MGMT_STATUS_MSG_RESOURCE
        CDA_ACTUATOR_CMD_RESOURCE
        CDA_SENSOR_MSG_RESOURCE
        CDA_SYSTEM_PERF_MSG_RESOURCE
        GDA_MGMT_STATUS_MSG_RESOURCE
        GDA_ACTUATOR_CMD_RESOURCE
        GDA_SENSOR_MSG_RESOURCE
    }

    class ConfigConst {
        <<constant>>
        ENABLE_MQTT_CLIENT_KEY
        DEFAULT_QOS
        DEFAULT_MQTT_PORT
        KEEP_ALIVE_KEY
        HOST_KEY
        PORT_KEY
    }

    class ActuatorData {
        -name: str
        -typeID: int
        -timeStamp: str
        -statusCode: int
        -hasError: bool
        -command: int
        -value: float
        +setCommand(command)
        +getCommand() int
        +setValue(value)
        +getValue() float
    }

    class SensorData {
        -name: str
        -typeID: int
        -timeStamp: str
        -statusCode: int
        -hasError: bool
        -value: float
        +setValue(value)
        +getValue() float
    }

    class SystemPerformanceData {
        -name: str
        -typeID: int
        -timeStamp: str
        -statusCode: int
        -cpuUtil: float
        -memUtil: float
        +setCpuUtilization(util)
        +setMemoryUtilization(util)
    }

    class DataUtil {
        +actuatorDataToJson(data) str
        +sensorDataToJson(data) str
        +systemPerformanceDataToJson(data) str
        +jsonToActuatorData(jsonData) ActuatorData
        +jsonToSensorData(jsonData) SensorData
        +jsonToSystemPerformanceData(jsonData) SystemPerformanceData
    }

    class ConfigUtil {
        +getProperty(section, key, default) str
        +getInteger(section, key, default) int
        +getFloat(section, key, default) float
        +getBoolean(section, key) bool
    }

    IPubSubClient <|.. MqttClientConnector
    IDataMessageListener <|.. DeviceDataManager
    DeviceDataManager --> MqttClientConnector
    DeviceDataManager --> SystemPerformanceManager
    DeviceDataManager --> SensorAdapterManager
    DeviceDataManager --> ActuatorAdapterManager
    MqttClientConnector --> ConfigUtil
    MqttClientConnector --> IDataMessageListener
    DeviceDataManager --> ConfigUtil
    DeviceDataManager --> ActuatorData
    DeviceDataManager --> SensorData
    DeviceDataManager --> SystemPerformanceData
    MqttClientConnector --> ResourceNameEnum
    DeviceDataManager --> ResourceNameEnum
    DataUtil --> ActuatorData
    DataUtil --> SensorData
    DataUtil --> SystemPerformanceData
```


## Testing

### Unit Tests
Run individual MQTT client tests:
```bash
python -m pytest tests/integration/connection/test_MqttClientConnector.py -v
```

### Available Tests
- testConnectAndDisconnect: Basic connection/disconnection
- testConnectAndCDAManagementStatusPubSub: Pub/Sub to management status topic
- testNewActuatorCmdPubSub: Actuator command pub/sub with JSON
- testActuatorCmdPubSub: Actuator command with specific value
- testSensorMsgPub: Sensor data publication
- testCDAManagementStatusSubscribe: Subscribe to management commands
- testCDAActuatorCmdSubscribe: Subscribe to actuator commands (300s)
- testCDAManagementStatusPublish: Publish management status

### Integration Test
Run the full CDA with MQTT:
```bash
python programmingtheiot/cda/app/ConstrainedDeviceApp.py
```

Expected output:
- MQTT client created and connected
- Subscribed to actuator command topic
- System performance data collected every 5 seconds
- CDA ready to receive actuator commands via MQTT

## Environment

- **Python:** 3.12.3
- **Libraries:** paho-mqtt, APScheduler, psutil, numpy
- **MQTT Broker:** Mosquitto 2.0.18
- **Testing:** pytest, unittest

## Test Results

- Ran 2 tests in 140.180s
- Status: OK (2 passed, 6 skipped)
- MQTT connectivity verified
- Pub/sub messaging verified
- QoS validation verified
- Message waiting (wait_for_publish) verified

## References

- [MQTT 3.1.1 Specification](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.html)
- [Paho Python Client](https://github.com/eclipse/paho.mqtt.python)
- Programming the Internet of Things - Chapter 6
