# HW3 SPRC

## Project Overview

This project implements an IoT monitoring architecture using Docker Swarm technology. It builds a stack of services that handle the logic of collecting, storing, and visualizing numerical data from a significant number of network-connected devices.

## Solution Stack

The solution stack consists of 4 key services:

1. MQTT Broker
2. InfluxDB Database
3. MQTT Adapter
4. Grafana Instance

## Execution

- Run the solution: `./run.sh`
- Clean up (delete all local images/volumes): `./clean.sh`

## Implementation Details

### 1. MQTT Broker

- Awaits IoT device connections on port 1883
- Configuration file: `mosquitto_mqtt_vol/mosquitto.conf`
- Authentication options:
  - Unauthorized clients allowed or not (user-configurable)
  - If auth required:
    - Account 1: username = writer, password = writer_sprc
    - Account 2: username = reader, password = reader_sprc
- Network: `broker_adaptor_network`
- File storage: `mosquitto_mqtt_vol` directory

### 2. InfluxDB Database

- Configured using `database/init_influx_db.sh`
- Database name: IoT_Devices (unlimited data retention)
- File storage: `database/influx_db` directory
- Networks:
  - `adaptor_influx_network`
  - `influx_grafana_network`

### 3. MQTT Adapter

- Implemented in Python (paho.mqtt.client package)
- Location: `adapter` directory
- Dockerfile available for image building
- OOP implementation in `adapter.py`
- Uses:
  - MQTT client to connect to the broker
  - InfluxDB client to communicate with the database
- Regular expression for topic validation

### 4. Grafana Instance

- Accessible on port 80
- Web browser interface
- 2 predefined dashboards (JSON format)
- Dashboard location: `grafana_db/grafana_provisioning/grafana_dashboards`
- Configuration files: `grafana_db/grafana_provisioning/*.yml`
- Features:
  - Regular expressions for dynamic device and metric handling
  - Compliant with project restrictions

## Additional Notes

- The adapter connects to the broker using the 'reader' account credentials
- The adapter processes and adds information to the InfluxDB database
- Grafana dashboards are capable of handling new devices and metrics dynamically
