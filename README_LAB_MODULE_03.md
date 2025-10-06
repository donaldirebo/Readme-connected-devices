# Lab Module 03: Data Simulation - README

## Overview

Lab Module 03 implements a comprehensive data simulation system for the Constrained Device Application (CDA). This module introduces simulated sensor data generation, actuator command processing, and automated threshold-based actuation responses.

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