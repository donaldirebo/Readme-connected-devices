# Constrained Device App (CDA) - Lab Module 02 Test Results

## Overview

This document contains the test results and outputs for Lab Module 02 system performance monitoring implementation in the CDA Python application.

## Test Environment

- **Python Version:** 3.12.3
- **Virtual Environment:** Active (venv)
- **Testing Framework:** pytest / Python unittest
- **IDE:** Eclipse with PyDev
- **OS:** Linux (Ubuntu)
- **Timezone:** America/Toronto
- **Date:** October 6, 2025

## Tests Executed

### 1. test_SystemPerformanceManager.py

**Purpose:** Integration test for SystemPerformanceManager - verifies scheduling, telemetry collection, and lifecycle management.

**Location:** `tests/integration/system/test_SystemPerformanceManager.py`

**Results:**
```
Status: OK (PASSED)
Tests Run: 1
Execution Time: 60.012 seconds
```

**Console Output:**
```
2025-10-06 18:04:10,626:test_SystemPerformanceManager:INFO:Testing SystemPerformanceManager class...
2025-10-06 18:04:10,626:ConfigUtil:INFO:Loading default config: /home/donald/piot/cda-python-components/config/PiotConfig.props
2025-10-06 18:04:10,627:ConfigUtil:INFO:Config file successfully loaded from path: /home/donald/piot/cda-python-components/config/PiotConfig.props
2025-10-06 18:04:10,630:SystemCpuUtilTask:INFO:Initialized SystemCpuUtilTask with name: DeviceCpuUtil, typeID: 9001
2025-10-06 18:04:10,631:SystemPerformanceManager:INFO:Starting SystemPerformanceManager...
2025-10-06 18:04:10,632:base:INFO:Scheduler started
2025-10-06 18:04:10,632:SystemPerformanceManager:INFO:Started SystemPerformanceManager.
```

**Telemetry Collection (Every 5 seconds):**
```
2025-10-06 18:04:15,633:SystemCpuUtilTask:DEBUG:CPU Utilization: 0.0%
2025-10-06 18:04:15,634:SystemPerformanceManager:DEBUG:CPU utilization is 0.0 percent, and memory utilization is 70.2 percent.

2025-10-06 18:04:20,633:SystemCpuUtilTask:DEBUG:CPU Utilization: 19.8%
2025-10-06 18:04:20,633:SystemPerformanceManager:DEBUG:CPU utilization is 19.8 percent, and memory utilization is 70.1 percent.

2025-10-06 18:04:25,633:SystemCpuUtilTask:DEBUG:CPU Utilization: 11.1%
2025-10-06 18:04:25,633:SystemPerformanceManager:DEBUG:CPU utilization is 11.1 percent, and memory utilization is 70.1 percent.

2025-10-06 18:04:30,632:SystemCpuUtilTask:DEBUG:CPU Utilization: 16.7%
2025-10-06 18:04:30,632:SystemPerformanceManager:DEBUG:CPU utilization is 16.7 percent, and memory utilization is 70.1 percent.

2025-10-06 18:04:35,631:SystemCpuUtilTask:DEBUG:CPU Utilization: 22.6%
2025-10-06 18:04:35,631:SystemPerformanceManager:DEBUG:CPU utilization is 22.6 percent, and memory utilization is 70.4 percent.

2025-10-06 18:04:40,632:SystemCpuUtilTask:DEBUG:CPU Utilization: 6.3%
2025-10-06 18:04:40,632:SystemPerformanceManager:DEBUG:CPU utilization is 6.3 percent, and memory utilization is 70.2 percent.

2025-10-06 18:04:45,633:SystemCpuUtilTask:DEBUG:CPU Utilization: 12.6%
2025-10-06 18:04:45,633:SystemPerformanceManager:DEBUG:CPU utilization is 12.6 percent, and memory utilization is 70.1 percent.

2025-10-06 18:04:50,632:SystemCpuUtilTask:DEBUG:CPU Utilization: 24.1%
2025-10-06 18:04:50,633:SystemPerformanceManager:DEBUG:CPU utilization is 24.1 percent, and memory utilization is 70.1 percent.

2025-10-06 18:04:55,632:SystemCpuUtilTask:DEBUG:CPU Utilization: 9.9%
2025-10-06 18:04:55,632:SystemPerformanceManager:DEBUG:CPU utilization is 9.9 percent, and memory utilization is 70.2 percent.

2025-10-06 18:05:00,631:SystemCpuUtilTask:DEBUG:CPU Utilization: 14.8%
2025-10-06 18:05:00,631:SystemPerformanceManager:DEBUG:CPU utilization is 14.8 percent, and memory utilization is 70.3 percent.

2025-10-06 18:05:05,632:SystemCpuUtilTask:DEBUG:CPU Utilization: 17.6%
2025-10-06 18:05:05,633:SystemPerformanceManager:DEBUG:CPU utilization is 17.6 percent, and memory utilization is 70.2 percent.

2025-10-06 18:05:10,634:SystemCpuUtilTask:DEBUG:CPU Utilization: 14.9%
2025-10-06 18:05:10,635:SystemPerformanceManager:DEBUG:CPU utilization is 14.9 percent, and memory utilization is 70.3 percent.
```

**Shutdown:**
```
2025-10-06 18:05:10,633:SystemPerformanceManager:INFO:Stopping SystemPerformanceManager...
2025-10-06 18:05:10,636:base:INFO:Scheduler has been shut down
2025-10-06 18:05:10,637:SystemPerformanceManager:INFO:Stopped SystemPerformanceManager.
```

**Verification:**
- APScheduler started and managed background job execution
- Telemetry collected 12 times (every 5 seconds over 60 seconds)
- CPU utilization ranged from 0.0% to 24.1%
- Memory utilization remained steady around 70.1-70.4%
- Clean shutdown with no errors

---

### 2. test_SystemCpuUtilTask.py

**Purpose:** Unit test for CPU utilization monitoring using psutil library.

**Location:** `tests/unit/system/test_SystemCpuUtilTask.py`

**Results:**
```
Status: OK (PASSED)
Tests Run: 1
Execution Time: 0.001 seconds
```

**Console Output:**
```
2025-10-06 18:08:15,128:test_SystemCpuUtilTask:INFO:Testing SystemCpuUtilTask class...
2025-10-06 18:08:15,128:SystemCpuUtilTask:INFO:Initialized SystemCpuUtilTask with name: DeviceCpuUtil, typeID: 9001
2025-10-06 18:08:15,128:SystemCpuUtilTask:DEBUG:CPU Utilization: 0.0%
2025-10-06 18:08:15,128:test_SystemCpuUtilTask:INFO:CPU utilization: 0.0
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

**Verification:**
- SystemCpuUtilTask initialized correctly
- Successfully retrieved CPU metrics using psutil.cpu_percent()
- Returned CPU utilization value of 0.0%
- Task completed in 1 millisecond

---

### 3. test_SystemMemUtilTask.py

**Purpose:** Unit test for memory utilization monitoring using psutil library.

**Location:** `tests/unit/system/test_SystemMemUtilTask.py`

**Results:**
```
Status: OK (PASSED)
Tests Run: 1
Execution Time: 0.001 seconds
```

**Console Output:**
```
2025-10-06 18:09:17,136:test_SystemMemUtilTask:INFO:Testing SystemMemUtilTask class...
2025-10-06 18:09:17,137:test_SystemMemUtilTask:INFO:Virtual memory utilization: 68.9
----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

**Verification:**
- SystemMemUtilTask initialized correctly
- Successfully retrieved memory metrics using psutil.virtual_memory().percent
- Returned memory utilization value of 68.9%
- Task completed in 1 millisecond

---

### 4. test_ConstrainedDeviceApp.py

**Purpose:** Integration test for main CDA application lifecycle and component initialization.

**Location:** `tests/integration/app/test_ConstrainedDeviceApp.py`

**Results:**
```
Status: OK (PASSED)
Tests Run: 1
Execution Time: 0.008 seconds
```

**Console Output:**
```
2025-10-06 18:10:35,099:root:INFO:Testing ConstrainedDeviceApp class...
2025-10-06 18:10:35,099:root:INFO:Initializing CDA...
2025-10-06 18:10:35,099:root:INFO:Loading default config: /home/donald/piot/cda-python-components/config/PiotConfig.props
2025-10-06 18:10:35,100:root:INFO:Config file successfully loaded from path: /home/donald/piot/cda-python-components/config/PiotConfig.props
2025-10-06 18:10:35,104:root:INFO:Initialized SystemCpuUtilTask with name: DeviceCpuUtil, typeID: 9001
2025-10-06 18:10:35,104:root:INFO:Local system performance tracking enabled
2025-10-06 18:10:35,104:root:INFO:Using simulators for actuator commands.
2025-10-06 18:10:35,104:root:INFO:Local actuation capabilities enabled
2025-10-06 18:10:35,104:root:INFO:Starting CDA...
2025-10-06 18:10:35,104:root:INFO:Starting DeviceDataManager...
2025-10-06 18:10:35,105:root:INFO:Starting SystemPerformanceManager...
2025-10-06 18:10:35,105:apscheduler.scheduler:INFO:Added job "SystemPerformanceManager.handleTelemetry" to job store "default"
2025-10-06 18:10:35,105:apscheduler.scheduler:INFO:Scheduler started
2025-10-06 18:10:35,106:root:INFO:Started SystemPerformanceManager.
2025-10-06 18:10:35,106:root:INFO:Started DeviceDataManager.
2025-10-06 18:10:35,106:root:INFO:CDA started.
2025-10-06 18:10:35,106:root:INFO:CDA stopping...
2025-10-06 18:10:35,106:root:INFO:Stopping DeviceDataManager...
2025-10-06 18:10:35,106:root:INFO:Stopping SystemPerformanceManager...
2025-10-06 18:10:35,106:apscheduler.scheduler:INFO:Scheduler has been shut down
2025-10-06 18:10:35,107:root:INFO:Stopped SystemPerformanceManager.
2025-10-06 18:10:35,107:root:INFO:Stopped DeviceDataManager.
2025-10-06 18:10:35,107:root:INFO:CDA stopped with exit code 0.
----------------------------------------------------------------------
Ran 1 test in 0.008s

OK
```

**Verification:**
- CDA initialized successfully
- Configuration loaded from PiotConfig.props
- SystemPerformanceManager initialized and started
- DeviceDataManager initialized and started
- APScheduler job added and scheduler started
- Clean shutdown sequence executed
- Application stopped with exit code 0
- All components stopped cleanly

---

## Summary

### Overall Results

| Test Name | Tests Run | Status | Duration | CPU Range | Memory Range |
|-----------|-----------|--------|----------|-----------|--------------|
| test_SystemPerformanceManager | 1 | PASSED | 60.012s | 0.0% - 24.1% | 70.1% - 70.4% |
| test_SystemCpuUtilTask | 1 | PASSED | 0.001s | 0.0% | N/A |
| test_SystemMemUtilTask | 1 | PASSED | 0.001s | N/A | 68.9% |
| test_ConstrainedDeviceApp | 1 | PASSED | 0.008s | N/A | N/A |
| **TOTAL** | **4** | **ALL PASSED** | **~60s** | - | - |

### Success Rate: 100%

All Lab Module 02 required tests passed successfully with correct outputs showing real-time system telemetry collection.

## Components Tested

1. **ConstrainedDeviceApp** - Main application lifecycle management
2. **SystemPerformanceManager** - Task scheduling using APScheduler
3. **BaseSystemUtilTask** - Base class for telemetry tasks
4. **SystemCpuUtilTask** - CPU utilization monitoring via psutil
5. **SystemMemUtilTask** - Memory utilization monitoring via psutil
6. **ConfigUtil** - Configuration file management

## Key Technologies

- **psutil** - Cross-platform library for system and process utilities
- **APScheduler** - Advanced Python Scheduler for background job execution
- **Python logging** - Built-in logging framework
- **pytest/unittest** - Python testing frameworks

## Telemetry Collection Details

The SystemPerformanceManager test demonstrated:
- **Collection Interval:** 5 seconds (configurable via PiotConfig.props)
- **Total Collections:** 12 samples over 60 seconds
- **CPU Utilization Statistics:**
  - Minimum: 0.0%
  - Maximum: 24.1%
  - Average: ~14.2%
- **Memory Utilization Statistics:**
  - Minimum: 70.1%
  - Maximum: 70.4%
  - Average: ~70.2%

## Running the Tests

### Activate Virtual Environment
```bash
cd /home/donald/piot/cda-python-components
source venv/bin/activate
```

### Run Individual Test
```bash
python3 -m pytest tests/integration/system/test_SystemPerformanceManager.py -v
```

### Run All Unit Tests
```bash
python3 -m pytest tests/unit/system/ -v
```

### Run All Integration Tests
```bash
python3 -m pytest tests/integration/ -v
```

### Run All Tests
```bash
python3 -m pytest tests/ -v
```

### In Eclipse with PyDev
1. Right-click on test file
2. Select "Run As" → "Python unit-test"
3. View results in Console panel
4. Check for "OK" status indicating all tests passed

## Notes

- Tests use psutil for cross-platform system metrics
- CPU values represent percentage of CPU usage (0-100%)
- Memory values represent percentage of virtual memory usage
- APScheduler runs telemetry collection as background jobs
- All tests include proper initialization and cleanup
- Configuration loaded from `config/PiotConfig.props`

## Comparison with GDA

Both implementations successfully monitor system performance, with key differences:

| Feature | CDA (Python) | GDA (Java) |
|---------|--------------|------------|
| Scheduler | APScheduler | ScheduledExecutorService |
| System Metrics | psutil library | JMX ManagementFactory |
| CPU Metric | CPU percentage | System load average |
| Memory Metric | Virtual memory % | JVM heap memory % |
| Language | Python 3.12.3 | Java 17 |

## References

- Lab Module 02 Requirements: [PIOT-CDA-02-001 through PIOT-CDA-02-007]
- Programming the Internet of Things by Andrew D. King
- Repository: github.com/donald4u/cda-python-components
- psutil Documentation: https://psutil.readthedocs.io/
- APScheduler Documentation: https://apscheduler.readthedocs.io/