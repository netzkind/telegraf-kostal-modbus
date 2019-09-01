# telegraf-kostal-modbus

Read data from Kostals Plenticore hybrid inverter and make it available as a Grafana dashboard.

## Running

To run the project as is, you will need to have a running Docker-setup available. If you do not already have one,
please check www.docker.com on how to get started.

Once you have Docker, starting this project is as simple as `docker-compose up`.

### cherry-picking

If you don't want to run this project and only want to cherry-pick configurations:

#### Telegraf + Modbus for Kostal

Please see the folder `telegraf-kostal-plenticore` for the:
* `Dockerfile` on how to create a running telegraf with Modbus-TCP
* `telegraf.conf` on how to configure telegraf with Modbus-TCP for Kostal Plenticore

#### Grafana-Dashboard for Kostal

Please see the folder `grafana/dashboards` for a JSON-file containing a dashboard for the above telegraf configuration.
Naming of fields with the telegraf plugin for Modbus is a little special, so the dashboard will be of no use to any
other setup, i suppose. 

## Configuration

If you choose to run the project as-is, you can do all of the configuration in `docker-compose.yaml`.
* Please edit the line `- "plenticore.local:10.7.77.10"` so the IP matches the IP of your Plenticore.
* Please edit the two lines with `- INFLUXDB_ADMIN_PASSWORD=secret` to change the admin-password for influxdb

### Configuring the dashboard

Once Grafana is up and running, you can reach it at Port 3000 on the IP of your Docker node (When running with Docker
Toolbox on Windows, thats http://192.168.99.100:3000).

After accessing Grafana, you can also edit the contained Dashboard ("Kostal Plenticore"). To do so, please log into
Grafana with the user "admin" and the password from the docker-compose.yaml line "GF_SECURITY_ADMIN_PASSWORD=secret".

* The dashboard contains variables for the price of consumption and feed in. You may want to change these
* The dashboard contains a gauge-diagram for "Erzeugung" which is by default set to "9900 Wp". Please feel free to edit 
this
* The dashboard is provisioned into the docker image and may not be saved. Please use "Save as" instead once to create
a copy of your own


## Where's the data stored?

By default, this project stores the data in so-called docker volumes. There are different ways on how to backup and
restore that data.

Another useful approach is to not use volumes, but to mount directories from the host into the containers. Please see
the Docker-documentation on how to do this.