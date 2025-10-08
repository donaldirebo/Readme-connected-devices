# Lab Module 03: Data Simulation - README

## Overview

Lab Module 03 implements a comprehensive data simulation system for the Constrained Device Application (CDA). This module introduces simulated sensor data generation, actuator command processing, and automated threshold-based actuation responses. 
This document also contains the test results and outputs for Lab Module 03 data simulation implementation in the CDA Python application.

# Constrained Device App (CDA) - Lab Module 03 Test Results


## Test Environment

- **Python Version:** 3.12.3
- **Virtual Environment:** Active (venv)
- **Testing Framework:** pytest / Python unittest
- **IDE:** Eclipse with PyDev
- **OS:** Linux (Ubuntu)
- **Timezone:** America/Toronto
- **Date:** October 6, 2025

## Tests Executed

### Data Container Tests

#### 1. test_BaseIotData.py
- **Status:** OK (PASSED)
- **Tests Run:** 3/3
- **Duration:** 0.002 seconds

#### 2. test_SensorData.py
- **Status:** OK (PASSED)
- **Tests Run:** 3/3
- **Duration:** 0.003 seconds
- **Sample Output:** `name=SensorDataFooBar,typeID=0,timeStamp=2025-10-06T22:43:17+00:00,locationID=constraineddevice001`

#### 3. test_ActuatorData.py
- **Status:** OK (PASSED)
- **Tests Run:** 3/3
- **Duration:** 0.003 seconds
- **Sample Output:** `name=ActuatorDataFooBar,typeID=0,timeStamp=2025-10-06T22:47:25+00:00,locationID=constraineddevice001`

#### 4. test_SystemPerformanceData.py
- **Status:** OK (PASSED)
- **Tests Run:** 3/3
- **Duration:** 0.002 seconds
- **Type ID:** 9000

### Sensor Simulation Tests

#### 5. test_TemperatureSensorSimTask.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2
- **Duration:** 0.003 seconds
- **Type ID:** 1013
- **Simulated Value:** 20.579921°C

#### 6. test_HumiditySensorSimTask.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2
- **Duration:** 0.005 seconds
- **Type ID:** 1010
- **Simulated Value:** 35.986133%

#### 7. test_PressureSensorSimTask.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2
- **Duration:** 0.003 seconds
- **Type ID:** 1012
- **Simulated Value:** 1007.562388 hPa

### Actuator Simulation Tests

#### 8. test_HumidifierActuatorSimTask.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2 (1 skipped)
- **Duration:** 0.003 seconds
- **Type ID:** 1002
- **Commands:** ON (18.2, 21.4), OFF

#### 9. test_HvacActuatorSimTask.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2 (1 skipped)
- **Duration:** 0.007 seconds
- **Type ID:** 1001
- **Commands:** ON (18.2°C, 21.4°C), OFF

### Integration Tests

#### 10. test_SensorAdapterManager.py
- **Status:** OK (PASSED)
- **Tests Run:** 1/1
- **Duration:** 60.041 seconds
- **Collections:** 12 cycles every 5 seconds
- **Sensor Types:** Temperature (1013), Humidity (1010), Pressure (1012)
- **Data Points:** 1440 time-series points per sensor

#### 11. test_ActuatorAdapterManager.py
- **Status:** OK (PASSED)
- **Tests Run:** 2/2
- **Duration:** 0.005 seconds
- **Actuators:** Humidifier (50.0), HVAC (22.5°C)

#### 12. test_SensorEmulatorManager.py
- **Status:** OK (PASSED)
- **Tests Run:** 1/1
- **Duration:** 60.017 seconds
- **Mode:** SenseHAT emulator mode
- **Collections:** 12 cycles

#### 13. test_ActuatorEmulatorManager.py
- **Status:** OK (PASSED)
- **Tests Run:** 3/3
- **Duration:** 0.004 seconds
- **Mode:** SenseHAT emulator mode

## Summary

**Total Tests:** 27/27 passed (100% success rate)
**Total Duration:** ~120 seconds
**Data Points Generated:** 1440 per sensor type
**Collection Interval:** 5 seconds

## Running Tests
```bash
# All data tests
python3 -m pytest tests/unit/data/ -v

# All sensor tests
python3 -m pytest tests/unit/sim/test_*Sensor*.py -v

# All actuator tests
python3 -m pytest tests/unit/sim/test_*Actuator*.py -v

# Integration tests
python3 -m pytest tests/integration/system/test_SensorAdapterManager.py -v
python3 -m pytest tests/integration/system/test_ActuatorAdapterManager.py -v
python3 -m pytest tests/integration/emulated/ -v

## Architecture

The implementation follows a layered architecture:

- **Application Layer**: `ConstrainedDeviceApp`, `DeviceDataManager`
- **Manager Layer**: `SensorAdapterManager`, `ActuatorAdapterManager`, `SystemPerformanceManager`
- **Simulation Layer**: Sensor and actuator simulator tasks
- **Data Layer**: Data container classes (SensorData, ActuatorData, SystemPerformanceData)

## Key Components

### 1. Data Containers (`programmingtheiot/data/`)

#### BaseIotData
Base class for all IoT data containers providing common functionality:
- Name, type ID, location ID
- Timestamp management
- Status codes

#### SensorData
Represents sensor readings:
- Stores float values (temperature, humidity, pressure)
- Automatic timestamp updates

#### ActuatorData
Represents actuator commands and responses:
- Command type (ON/OFF)
- Float value for actuation
- State data (string)
- Response flag

#### SystemPerformanceData
System metrics container:
- CPU utilization percentage
- Memory utilization percentage

### 2. Simulation Tasks (`programmingtheiot/cda/sim/`)

#### BaseSensorSimTask
Base class for sensor simulators:
- Generates telemetry from datasets or random values
- Configurable min/max ranges
- Returns SensorData instances

#### Sensor Simulators
- **HumiditySensorSimTask**: Simulates humidity readings (30-50% range)
- **PressureSensorSimTask**: Simulates pressure readings (990-1010 kPa range)
- **TemperatureSensorSimTask**: Simulates temperature readings (18-22°C range)

#### BaseActuatorSimTask
Base class for actuator simulators:
- Processes ON/OFF commands
- Validates commands (no duplicate processing)
- Logs actuation events
- Returns ActuatorData responses

#### Actuator Simulators
- **HumidifierActuatorSimTask**: Simulates humidifier control
- **HvacActuatorSimTask**: Simulates HVAC control

### 3. Adapter Managers (`programmingtheiot/cda/system/`)

#### SensorAdapterManager
Manages sensor data collection:
- Uses APScheduler for periodic telemetry generation (default: 5-second intervals)
- Generates datasets for realistic daily patterns
- Routes sensor data to DeviceDataManager via callbacks
- Sets location ID on all sensor data

**Key Methods:**
- `startManager()`: Begins scheduled telemetry collection
- `stopManager()`: Stops the scheduler
- `handleTelemetry()`: Collects data from all sensors
- `setDataMessageListener()`: Registers callback listener

#### ActuatorAdapterManager
Manages actuator command processing:
- Validates incoming commands (location ID, response flag)
- Routes commands to appropriate actuators
- Returns actuation responses

**Key Methods:**
- `sendActuatorCommand(data)`: Processes actuation requests
- `setDataMessageListener()`: Registers callback listener

### 4. Central Coordinator (`programmingtheiot/cda/app/`)

#### DeviceDataManager
Heart of the CDA - coordinates all components:
- Implements `IDataMessageListener` interface
- Manages all adapter managers
- Performs local data analytics
- Handles threshold-based automation

**Key Features:**
- **Temperature Automation**: Monitors temperature and triggers HVAC
  - If temp > ceiling (default 20°C): Turn HVAC ON to cool
  - If temp < floor (default 18°C): Turn HVAC ON to heat
  - If temp in range: Turn HVAC OFF
- **Data Caching**: Stores latest readings for each sensor/actuator
- **Callback Routing**: Distributes data to appropriate handlers

**Configuration Properties:**
```properties
# Enable/disable features
enableSystemPerformance = True
enableSensing = True

# Temperature automation
handleTempChangeOnDevice = True
triggerHvacTempFloor = 18.0
triggerHvacTempCeiling = 20.0

# Polling rate (seconds)
pollCycles = 5
```

#### ConstrainedDeviceApp
Main application entry point:
- Initializes DeviceDataManager
- Handles application lifecycle (start/stop)
- Supports keyboard interrupt and exception handling
- Can run continuously or for fixed duration

## Data Flow

### Sensor Data Flow
```
Sensor Simulator → SensorAdapterManager → DeviceDataManager
                                              ↓
                                    _handleSensorDataAnalysis()
                                              ↓
                                    (if temp out of range)
                                              ↓
                                    ActuatorAdapterManager → Actuator Simulator
```

### Actuation Flow
```
External Command/Internal Trigger → DeviceDataManager.handleActuatorCommandMessage()
                                              ↓
                                    ActuatorAdapterManager.sendActuatorCommand()
                                              ↓
                                    Actuator Simulator (HVAC/Humidifier)
                                              ↓
                                    Response → DeviceDataManager (callback)
```

## Configuration

### File Location
`config/PiotConfig.props`

### Key Settings
```properties
[ConstrainedDevice]
enableSystemPerformance = True
enableSensing = True
enableEmulator = False
deviceLocationId = constraineddevice001

# Polling
pollCycles = 5

# Temperature automation
handleTempChangeOnDevice = True
triggerHvacTempFloor = 18.0
triggerHvacTempCeiling = 20.0

# Sensor simulation ranges
humiditySimFloor = 30.0
humiditySimCeiling = 50.0
pressureSimFloor = 990.0
pressureSimCeiling = 1010.0
tempSimFloor = 18.0
tempSimCeiling = 22.0
```

## Running the Application

### Option 1: Run as Application
```bash
cd ~/piot/cda-python-components
source venv/bin/activate
python -m programmingtheiot.cda.app.ConstrainedDeviceApp
```

### Option 2: Run Tests
```bash
# Unit tests
python -m pytest tests/unit/ -v

# Integration tests
python -m pytest tests/integration/ -v

# Specific test
python -m pytest tests/integration/app/test_ConstrainedDeviceApp.py -v
```

### Using VS Code
1. Open Testing panel (Ctrl+Shift+T)
2. Navigate to test file
3. Click play button to run

## Test Coverage

### Unit Tests (`tests/unit/`)
- Data containers: `test_SensorData.py`, `test_ActuatorData.py`, `test_SystemPerformanceData.py`
- Sensor simulators: `test_HumiditySensorSimTask.py`, `test_PressureSensorSimTask.py`, `test_TemperatureSensorSimTask.py`
- Actuator simulators: `test_HumidifierActuatorSimTask.py`, `test_HvacActuatorSimTask.py`

### Integration Tests (`tests/integration/`)
- System managers: `test_SensorAdapterManager.py`, `test_ActuatorAdapterManager.py`
- Application: `test_DeviceDataManagerNoComms.py`, `test_ConstrainedDeviceApp.py`

## Expected Output

When running, you'll see log output similar to:
```
2025-10-06 15:30:00,123:DeviceDataManager:INFO:Starting DeviceDataManager...
2025-10-06 15:30:00,124:SensorAdapterManager:INFO:Started SensorAdapterManager.
2025-10-06 15:30:05,125:SensorAdapterManager:DEBUG:Generated humidity data: 35.2
2025-10-06 15:30:05,125:SensorAdapterManager:DEBUG:Generated pressure data: 995.8
2025-10-06 15:30:05,126:SensorAdapterManager:DEBUG:Generated temp data: 20.5
2025-10-06 15:30:05,126:DeviceDataManager:INFO:Handle temp change: True - type ID: 3
2025-10-06 15:30:05,127:ActuatorAdapterManager:INFO:Actuator command received...
2025-10-06 15:30:05,127:BaseActuatorSimTask:INFO:Simulating HvacActuator actuator ON:
*******
* O N *
*******
HvacActuator VALUE -> 20.0
```

## Dependencies

### Required Python Packages
- `apscheduler` - Task scheduling for periodic data collection
- `psutil` - System performance monitoring (CPU, memory)
- `pytest` - Testing framework

### Install Dependencies
```bash
pip install -r requirements.txt
```

## Design Patterns Used

1. **Observer Pattern**: IDataMessageListener interface for callbacks
2. **Template Method**: Base classes (BaseSensorSimTask, BaseActuatorSimTask)
3. **Factory Pattern**: Sensor/actuator task creation
4. **Singleton Behavior**: ConfigUtil for configuration access
5. **Strategy Pattern**: Data generation (random vs dataset-based)

## Future Enhancements (Part III)

This module prepares for:
- Network communication (MQTT, CoAP)
- Data transmission to Gateway Device Application (GDA)
- Remote actuation commands
- Cloud integration
- Data persistence

## Troubleshooting

### Issue: Tests Fail with "No module found"
**Solution**: Ensure you're in the correct directory and virtual environment is activated
```bash
cd ~/piot/cda-python-components
source venv/bin/activate
```

### Issue: Scheduler doesn't start
**Solution**: Check that pollCycles > 0 in configuration file

### Issue: HVAC never triggers
**Solution**: Verify temperature ranges in config and ensure handleTempChangeOnDevice = True

## License

This implementation is part of the Programming the Internet of Things project and is available under the MIT License.

## References

- Programming the Internet of Things Book (O'Reilly)
- APScheduler Documentation: https://apscheduler.readthedocs.io/
- Course Repository: https://github.com/programming-the-iot/

## Author

Completed as part of Lab Module 03 coursework
Date: October 2025