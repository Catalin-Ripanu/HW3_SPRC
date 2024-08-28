# HW3_SPRC

This project aims to implement an IoT monitoring architecture using Docker Swarm technology to build a stack of services that handle the logic of collecting, storing, and visualizing numerical data from a significant number of network-connected devices.

## Implementation

The solution stack contains 4 important services, namely: the MQTT broker using the latest version of the corresponding image, the InfluxDB type database with version 1.8 that doesn't require any authentication logic, the MQTT adapter implemented in Python and described in a Dockerfile in its directory, and the Grafana instance that allows visualization of data sent by the adapter through dashboards written in JSON and added in the grafana_db/grafana_provisioning/grafana_dashboards directory.

The solution can be executed using just one command, namely ./run.sh (this is a script that performs the entire setup). Also, deleting all local images/volumes created by run.sh can be done using the ./clean.sh command (this is a script that removes everything added by run.sh).

### 1.1 MQTT Broker

This broker awaits potential connections from IoT devices on port 1883. Moreover, it uses the configuration file mosquitto_mqtt_vol/mosquitto.conf to allow (or not) connections from unauthorized clients (who don't use a valid account, also, in the run.sh script there is a prompt for the solution user that modifies the broker's configuration file depending on what is typed).

If the 'yes' option is chosen, then the broker reads 2 other files that handle the configuration of the following 2 accounts: username = writer & password = writer_sprc, and username = reader & password = reader_sprc. The adapter connects to the broker using the credentials of the account with username = reader regardless of the user's option.

Also, it is in the broker_adaptor_network network together with the adapter so that it communicates only with it, and, last but not least, it was decided that the broker would save all its files in the aforementioned directory so as not to lose logs that could help in a potential crash situation.

### 1.2 InfluxDB Database

This service was configured using the bash script in the database/init_influx_db.sh directory that uses CLI commands to create the database called IoT_Devices with unlimited data retention. As above, all information sent during execution is kept in the database/influx_db directory by copying all files from /var/lib/influxdb (path located in the Docker container). Obviously, to limit communication with other containers, this service is in the adaptor_influx_network and influx_grafana_network networks so that it can communicate only with the adapter and the Grafana instance.

### 1.3 MQTT Adapter

This service was implemented in Python using the paho.mqtt.client package and is located in the adapter directory. Also, the image related to the adapter can be built using the Dockerfile file located in the same directory.

For the design of this entity, an OOP implementation coded in the adapter.py source was preferred, which uses an MQTT client to connect to the broker and an InfluxDB client to communicate with the database (the adapter is the one that adds the processed information to this database, not the clients that transmit data to the broker). To eliminate messages with incorrectly formatted topics, a regular expression was used to help reject them (using, obviously, the match function from the re package).

### 1.4 Grafana Instance

This service is accessible on port 80 and represents a graphical interface that can be activated using a web browser and which contains 2 predefined dashboards that illustrate the data coming from the stack's database. The dashboards were written in JSON and are located in the grafana_db/grafana_provisioning/grafana_dashboards directory (in grafana_db/grafana_provisioning there are also some .yml files used by Grafana for loading these dashboards and setting the data source).

Using regular expressions in the JSON sources, the 2 dashboards are capable of respecting the restrictions from the statement (such as adding new devices that connect and new metrics that appear).
