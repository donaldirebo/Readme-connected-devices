# CDA Lab Module 04 - Test Results

## Overview
Lab Module 04 implements data emulation using the Sense HAT Emulator, enabling real-time sensor data collection and LED display control through a virtual hardware interface.
## Code Repository and Branch
- https://github.com/donald4u/cda-python-components/tree/labmodule04

## Environment

- **Python:** 3.10.19 (venv-py310)
- **Emulator:** Sense HAT Emulator GUI
- **Date:** October 12, 2025

## Test Results

| Test | Status | Duration | Values |
|------|--------|----------|--------|
| Temperature Emulator | PASSED | 10.25s | 29.98-35.03°C |
| Humidity Emulator | PASSED | 10.02s | 45.10-45.51% |
| Pressure Emulator | PASSED | 10.02s | 983.60-983.61 mbar |
| HVAC Emulator | PASSED | 5.01s | 20°C, 22°C |
| Humidifier Emulator | PASSED | 5.01s | 35%, 50% |
| LED Display Emulator | PASSED | 65s | Scrolling text |

**Success Rate:** 100% (6/6 tests passed)

## Class Diagram

```mermaid
classDiagram
    class BaseSensorSimTask {
        <<abstract>>
        +generateTelemetry() SensorData
    }

    class BaseActuatorSimTask {
        <<abstract>>
        +updateActuator(ActuatorData) ActuatorData
    }

    class TemperatureSensorEmulatorTask {
        -SenseHAT sh
        +generateTelemetry() SensorData
    }

    class HumiditySensorEmulatorTask {
        -SenseHAT sh
        +generateTelemetry() SensorData
    }

    class PressureSensorEmulatorTask {
        -SenseHAT sh
        +generateTelemetry() SensorData
    }

    class HvacEmulatorTask {
        +_activateActuator() int
        +_deactivateActuator() int
    }

    class HumidifierEmulatorTask {
        +_activateActuator() int
        +_deactivateActuator() int
    }

    class LedDisplayEmulatorTask {
        -SenseHat sh
        +_activateActuator() int
        +_deactivateActuator() int
    }

    class SensorData {
        +String name
        +int typeID
        +float value
    }

    class ActuatorData {
        +String name
        +int typeID
        +int command
    }

    TemperatureSensorEmulatorTask --|> BaseSensorSimTask
    HumiditySensorEmulatorTask --|> BaseSensorSimTask
    PressureSensorEmulatorTask --|> BaseSensorSimTask
    
    HvacEmulatorTask --|> BaseActuatorSimTask
    HumidifierEmulatorTask --|> BaseActuatorSimTask
    LedDisplayEmulatorTask --|> BaseActuatorSimTask
    
    BaseSensorSimTask ..> SensorData : creates
    BaseActuatorSimTask ..> ActuatorData : uses
```
## All Tests

Sensor Emulator Tests:
- test_TemperatureEmulatorTask.py - PASSED (10.25s) - Read 29.98-35.03°C
- test_HumidityEmulatorTask.py - PASSED (10.02s) - Read 45.10-45.51%
- test_PressureEmulatorTask.py - PASSED (10.02s) - Read 983.60-983.61 mbar

Actuator Emulator Tests:
- test_HvacEmulatorTask.py - PASSED (5.01s) - Commands at 20°C, 22°C
- test_HumidifierEmulatorTask.py - PASSED (5.01s) - Commands at 35%, 50%
- test_LedDisplayEmulatorTask.py - PASSED (65s) - Scrolling text display
Total: 6 tests, all passed, ~105 seconds total runtime
Key Achievement: Successfully integrated with Sense HAT Emulator GUI, reading live sensor data and controlling LED display.

## Sample Outputs

**Temperature Sensor:**
```
TEMPERATURE READ FROM EMULATOR: 34.96875°C
TEMPERATURE READ FROM EMULATOR: 35.03125°C
```

**Humidity Sensor:**
```
SensorData: 45.507812 - name=HumiditySensor,typeID=1010
SensorData: 45.292969 - name=HumiditySensor,typeID=1010
```

**Pressure Sensor:**
```
SensorData: 983.599609 - name=PressureSensor,typeID=1012
SensorData: 983.607422 - name=PressureSensor,typeID=1012
```

**LED Display:**
```
LED MESSAGE: Hello, world!
LED MESSAGE: Welcome to Connected Devices!
LED DISPLAY CLEARED
```

## Implementation

**Sensor Reading:**
```python
self.sh = SenseHAT(emulate=True)
sensorVal = self.sh.environ.temperature  # or humidity, pressure
```

**LED Control:**
```python
from sense_emu import SenseHat
self.sh.show_message(msg, scroll_speed=0.05, text_colour=[255, 0, 0])
self.sh.clear()
```

## Setup

**Create Python 3.10 environment:**
```bash
python3.10 -m venv venv-py310
source venv-py310/bin/activate
pip install -r requirements.txt
```

**Install system dependencies:**
```bash
sudo apt install libgirepository1.0-dev gcc libcairo2-dev pkg-config
```

**Run emulator:**
```bash
sense_emu_gui &
```

## Running Tests

```bash
source venv-py310/bin/activate
python -m pytest tests/integration/emulated/test_*EmulatorTask.py -v
```

## References

- Lab Module 04: PIOT-CDA-04-001 through PIOT-CDA-04-005
- Repository: github.com/donald4u/cda-python-components

## Author

Donald - October 2025
