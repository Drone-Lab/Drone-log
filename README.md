# Drone Log Dataset

A collection of PX4 drone flight logs for anomaly detection research and analysis.

## Overview

This dataset contains flight logs from PX4-based drones, organized into **anomaly logs** and **benign logs**. Each log is stored in CSV format and can be used to study flight behavior, detect anomalies, or validate drone safety mechanisms.

## Directory Structure

```
Drone_log/
├── anomaly_logs/          # Flight logs containing known anomalies
│   ├── case1/
│   │   ├── logcsv/        # CSV flight data logs
│   │   └── case_description.md   # Detailed description of the anomaly
│   ├── case2/
│   ├── ...
│   └── case12/
│
└── benign_logs/           # Normal flight logs from various test scenarios
│   ├── get_geofence_test_0/
│   ├── get_global_position_test_1/
│   ├── get_home_position_test_3/
│   ├── get_local_position_test_5/
│   └── ...
```

## Anomaly Cases

The `anomaly_logs` directory contains documented flight anomalies. Each case includes:

- **`logcsv/`** — Raw flight data in CSV format (e.g., actuator outputs, battery status, position data)
- **`case_description.md`** — Scenario details, expected vs. observed behavior, and relevant telemetry fields

### Example: Case 1
- **Issue**: [PX4 #22373](https://github.com/PX4/PX4-Autopilot/issues/22373) — RTL returns into a restricted no-fly zone
- **Summary**: When the Home point is inside an exclusion zone, Return-to-Launch (RTL) does not revalidate the Home position and flies back into the restricted area.
- **Key Fields**: `current_flight_mode`, `current_lat/lon`, `rel_alt`, geofence `STATUSTEXT` messages

## Benign Logs

The `benign_logs` directory contains normal flight logs from various PX4 test scenarios, including:

- Geofence tests
- Global / local position tests
- Home position tests
- Land detection tests
- Mission result tests
- Position setpoint triplet tests

## Data Format

Flight logs are stored as timestamped CSV files. Typical log topics include:

- `actuator_armed` — Arming state
- `actuator_controls_status` — Control surface status
- `actuator_motors` / `actuator_outputs` — Motor commands and outputs
- `battery_status` — Battery voltage and current
- `vehicle_gps_position` — GPS coordinates
- `vehicle_local_position` — Local position estimates
- `vehicle_status` — Flight mode and system status
- `geofence_result` — Geofence violations

## Usage

These logs can be used for:

- Training/evaluating anomaly detection models
- Drone behavior analysis and visualization
- Validating PX4 autopilot safety features
- Research on UAV flight safety

## License

[Add your license here]

## Acknowledgments

- Logs generated using [PX4 Autopilot](https://px4.io/) SITL and real hardware
- Anomaly cases based on reported PX4 issues

