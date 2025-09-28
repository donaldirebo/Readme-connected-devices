Programming the Internet of Things (PIoT) – Gateway and Cloud Device Applications
Overview

This repository contains two core components of the Programming the Internet of Things (PIoT) project:

1. Gateway Device Application (GDA) – Java
   Runs on the local gateway device to monitor system performance, collect telemetry, and manage IoT devices locally.

2. Cloud Device Application (CDA) – Python
   Runs in the cloud to receive telemetry from gateways, store data, and provide remote management and analytics capabilities.

This design allows seamless integration between local monitoring and cloud-based data processing.
Features
GDA – Gateway Device Application (Java)
- Collects local system performance data:
  - CPU Utilization (SystemCpuUtilTask)
  - Memory Usage (SystemMemUtilTask)
- Sends telemetry to listeners via IDataMessageListener (console or network integration possible)
- Configurable via properties file (default: PiotConfig.props)
- Supports command-line arguments for custom configurations
- Modular: can extend with additional system metrics or IoT tasks
CDA – Cloud Device Application (Python)
- Receives telemetry from multiple GDA instances
- Stores or processes telemetry for analytics and dashboards
- Provides a flexible framework for cloud-side IoT processing
- Easily integrated with databases, MQTT brokers, or REST APIs

Project Structure
piot
├── gda-java-components      # Java-based Gateway Device App
│   ├── src/main/java/programmingtheiot/gda/app
│   │   ├── GatewayDeviceApp.java
│   │   └── ConsoleTelemetryListener.java
│   ├── src/main/java/programmingtheiot/gda/system
│   │   ├── SystemPerformanceManager.java
│   │   ├── SystemCpuUtilTask.java
│   │   └── SystemMemUtilTask.java
│   └── src/main/java/programmingtheiot/common
│       ├── ConfigConst.java
│       ├── ConfigUtil.java
│       └── IDataMessageListener.java
├── cda-python-components    # Python-based Cloud Device App
│   ├── cloud_device_app.py
│   └── cloud_telemetry_manager.py
├── docs                     # Diagram images
│   ├── gda_class_diagram.png
│   └── telemetry_sequence_diagram.png
├── pom.xml                  # Java project build file
└── README.md


How it Works

GDA Workflow (Java)
1. Startup: GatewayDeviceApp initializes configuration and SystemPerformanceManager. Command-line arguments can specify custom config files.
2. Monitoring: SystemPerformanceManager starts SystemCpuUtilTask and SystemMemUtilTask. Each task collects telemetry periodically and sends data to IDataMessageListener.
3. Telemetry: ConsoleTelemetryListener prints data to the console. Custom listeners can forward data to the CDA via MQTT, HTTP, or other protocols.
4. Shutdown: After the configured runtime, GDA stops all tasks cleanly.
CDA Workflow (Python)
1. Startup: cloud_device_app.py initializes CloudTelemetryManager.
2. Telemetry Handling: CloudTelemetryManager receives telemetry from GDA instances, stores it, and optionally processes it for analytics.
Diagrams
Class Diagram (GDA): See docs/gda_class_diagram.png
Sequence Diagram – Telemetry Flow (GDA → CDA): See docs/telemetry_sequence_diagram.png
Build & Run Instructions
GDA (Java)
cd gda-java-components
mvn clean install
mvn exec:java -Dexec.mainClass="programmingtheiot.gda.app.GatewayDeviceApp"
CDA (Python)
cd cda-python-components
python cloud_device_app.py
