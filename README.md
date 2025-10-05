# Lab Module 02: System Performance Monitoring

## Overview

Lab Module 02 adds system performance monitoring capabilities to both the Gateway Device App (GDA) and Constrained Device App (CDA). These applications now continuously collect and log CPU and memory utilization metrics at regular intervals.

## Objectives

- Build system performance monitoring into the GDA (Java) and CDA (Python)
- Collect CPU utilization metrics from the host system
- Collect memory utilization metrics from the host system
- Schedule telemetry collection at configurable intervals
- Log performance data for monitoring and analysis

## Implementation Details

### Gateway Device App (GDA) - Java

**Components Implemented:**

1. **SystemPerformanceManager** (`programmingtheiot.gda.system.SystemPerformanceManager`)
   - Manages scheduled telemetry collection
   - Uses `ScheduledExecutorService` for task scheduling
   - Configurable poll rate from configuration file
   - Starts and stops performance monitoring tasks

2. **BaseSystemUtilTask** (`programmingtheiot.gda.system.BaseSystemUtilTask`)
   - Abstract base class for system utilization tasks
   - Defines template method pattern for telemetry collection
   - Provides common functionality for task name and type ID

3. **SystemCpuUtilTask** (`programmingtheiot.gda.system.SystemCpuUtilTask`)
   - Extends `BaseSystemUtilTask`
   - Collects CPU utilization using `ManagementFactory.getOperatingSystemMXBean()`
   - Returns system load average as a float value

4. **SystemMemUtilTask** (`programmingtheiot.gda.system.SystemMemUtilTask`)
   - Extends `BaseSystemUtilTask`
   - Collects JVM heap memory utilization using `ManagementFactory.getMemoryMXBean()`
   - Calculates and returns memory usage percentage

5. **GatewayDeviceApp** (`programmingtheiot.gda.app.GatewayDeviceApp`)
   - Modified to integrate `SystemPerformanceManager`
   - Starts performance monitoring on app startup
   - Stops performance monitoring on app shutdown
   - Fixed `System.exit()` to only terminate on error conditions

**Key Technologies:**
- Java 17
- Java Management Extensions (JMX)
- ScheduledExecutorService for task scheduling
- Maven for build management

### Constrained Device App (CDA) - Python

**Components Implemented:**

1. **SystemPerformanceManager** (`programmingtheiot.cda.system.SystemPerformanceManager`)
   - Manages scheduled telemetry collection
   - Uses APScheduler's `BackgroundScheduler` for task scheduling
   - Configurable poll rate from configuration file
   - Starts and stops performance monitoring tasks

2. **BaseSystemUtilTask** (`programmingtheiot.cda.system.BaseSystemUtilTask`)
   - Base class for system utilization tasks
   - Defines template method pattern for telemetry collection
   - Provides common functionality for task name and type ID

3. **SystemCpuUtilTask** (`programmingtheiot.cda.system.SystemCpuUtilTask`)
   - Extends `BaseSystemUtilTask`
   - Collects CPU utilization using `psutil.cpu_percent()`
   - Returns CPU usage percentage as a float value

4. **SystemMemUtilTask** (`programmingtheiot.cda.system.SystemMemUtilTask`)
   - Extends `BaseSystemUtilTask`
   - Collects memory utilization using `psutil.virtual_memory().percent`
   - Returns memory usage percentage as a float value

5. **ConstrainedDeviceApp** (`programmingtheiot.cda.app.ConstrainedDeviceApp`)
   - Modified to integrate `SystemPerformanceManager`
   - Starts performance monitoring on app startup
   - Stops performance monitoring on app shutdown

**Key Technologies:**
- Python 3.12.3
- psutil library for system metrics
- APScheduler for task scheduling
- pytest for testing

## Architecture

### CDA Class Diagram

The following diagram shows the class structure and relationships in the CDA system performance monitoring implementation:
```mermaid
classDiagram
    class ConstrainedDeviceApp {
        -SystemPerformanceManager sysPerfMgr
        -bool isStarted
        +__init__()
        +bool isAppStarted()
        +void startApp()
        +void stopApp(int code)
    }

    class SystemPerformanceManager {
        -int pollRate
        -str locationID
        -IDataMessageListener dataMsgListener
        -BackgroundScheduler scheduler
        -SystemCpuUtilTask cpuUtilTask
        -SystemMemUtilTask memUtilTask
        +__init__()
        +void handleTelemetry()
        +void startManager()
        +void stopManager()
        +void setDataMessageListener(listener)
    }

    class BaseSystemUtilTask {
        #str name
        #int typeID
        +__init__(str name, int typeID)
        +str getName()
        +int getTypeID()
        +float getTelemetryValue()
    }

    class SystemCpuUtilTask {
        +__init__()
        +float getTelemetryValue()
    }

    class SystemMemUtilTask {
        +__init__()
        +float getTelemetryValue()
    }

    class ConfigUtil {
        <<singleton>>
        +__init__(str configFile)
        +getInstance() ConfigUtil
        +getInteger(str section, str key, int defaultVal) int
        +getProperty(str section, str key, str defaultVal) str
        +getBoolean(str section, str key) bool
    }

    class ConfigConst {
        <<module>>
        +DEFAULT_POLL_CYCLES: int
        +POLL_CYCLES_KEY: str
        +CONSTRAINED_DEVICE: str
        +DEVICE_LOCATION_ID_KEY: str
        +CPU_UTIL_NAME: str
        +MEM_UTIL_NAME: str
        +CPU_UTIL_TYPE: int
        +MEM_UTIL_TYPE: int
        +RUN_FOREVER_KEY: str
    }

    class BackgroundScheduler {
        <<APScheduler>>
        +add_job(func, trigger, seconds)
        +start()
        +shutdown()
    }

    class psutil {
        <<library>>
        +cpu_percent() float
        +virtual_memory() MemoryInfo
    }

    ConstrainedDeviceApp *-- SystemPerformanceManager : contains
    SystemPerformanceManager *-- SystemCpuUtilTask : contains
    SystemPerformanceManager *-- SystemMemUtilTask : contains
    SystemCpuUtilTask --|> BaseSystemUtilTask : extends
    SystemMemUtilTask --|> BaseSystemUtilTask : extends
    
    ConstrainedDeviceApp ..> ConfigUtil : uses
    SystemPerformanceManager ..> ConfigUtil : uses
    ConstrainedDeviceApp ..> ConfigConst : uses
    SystemPerformanceManager ..> ConfigConst : uses
    BaseSystemUtilTask ..> ConfigConst : uses
    
    SystemPerformanceManager ..> BackgroundScheduler : uses
    SystemCpuUtilTask ..> psutil : uses
    SystemMemUtilTask ..> psutil : uses

    note for ConstrainedDeviceApp "Main application entry point"
    note for SystemPerformanceManager "Coordinates system monitoring using APScheduler"
    note for BaseSystemUtilTask "Base class using Template Method pattern"
    note for SystemCpuUtilTask "Collects CPU utilization using psutil"
    note for SystemMemUtilTask "Collects memory utilization using psutil"
## Configuration

**Integration Tests:**
```bash
mvn test -Dtest=GatewayDeviceAppTest
mvn test -Dtest=SystemPerformanceManagerTest
```

### CDA Tests

Activate virtual environment first:
```bash
source venv/bin/activate
```

**Unit Tests:**
```bash
python3 -m pytest tests/unit/system/test_SystemCpuUtilTask.py -v
python3 -m pytest tests/unit/system/test_SystemMemUtilTask.py -v
```

**Integration Tests:**
```bash
python3 -m pytest tests/integration/system/test_SystemPerformanceManager.py -v
python3 -m pytest tests/integration/app/test_ConstrainedDeviceApp.py -v
```

## Test Results

**GDA:**
- ConfigUtilDefaultTest: ✓ Passed (6 tests)
- ConfigUtilCustomTest: ✓ Passed (7 tests)
- SystemCpuUtilTaskTest: ✓ Passed
- SystemMemUtilTaskTest: ✓ Passed
- SystemPerformanceManagerTest: ✓ Passed (60s runtime)
- GatewayDeviceAppTest: ✓ Passed (65s runtime)

**CDA:**
- test_SystemCpuUtilTask: ✓ Passed (0.03s)
- test_SystemMemUtilTask: ✓ Passed (0.03s)
- test_SystemPerformanceManager: ✓ Passed (60s runtime)
- test_ConstrainedDeviceApp: ✓ Passed (0.08s)

## Running the Applications

### GDA
```bash
cd gda-java-components
mvn compile
mvn exec:java -Dexec.mainClass="programmingtheiot.gda.app.GatewayDeviceApp"
```

### CDA
```bash
cd cda-python-components
source venv/bin/activate
python3 -m programmingtheiot.cda.app.ConstrainedDeviceApp
```

## Design Patterns Used

1. **Template Method Pattern:** `BaseSystemUtilTask` defines the structure, subclasses implement specific telemetry collection
2. **Manager Pattern:** `SystemPerformanceManager` coordinates multiple tasks
3. **Dependency Injection:** Configuration is injected via `ConfigUtil`

## Future Enhancements

Potential improvements for future lab modules:
- Add disk utilization monitoring
- Add network utilization monitoring
- Implement data persistence for telemetry
- Add alerting thresholds for critical resource usage
- Implement data visualization dashboards

## References

- Programming the Internet of Things by Andrew D. King (O'Reilly Media)
- Lab exercise tasks: https://github.com/programming-the-iot/book-exercise-tasks/issues/202

## Author

Donald Chinonso Irebo- October 2025