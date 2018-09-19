# TICK Stack

Run the complete TICK stack using this [docker-compose](https://docs.docker.com/compose/) file.
By using docker-compose all four official TICK stack images are started and linked together.
To know more about the individual components see [this](https://influxdata.com/)

There is also [this community Docker Swarm Compose file](https://gist.github.com/cdelaitre/85949d8b697359a319e30a678e23d8bd) as well.

## Usage

Start all the images as follows:

    # cd to desired version
    cd 1.3/
    # Start all images in the background
    docker-compose up -d

### Check that InfluxDB works:

Run this curl command, if no errors occur InfluxDB is running:

    curl http://localhost:8086/ping


#### The `influx` client

Use the built-in influx cli service:

    docker-compose run influxdb-cli

### Check that Telegraf works

By default, the Telegraf creates a `telegraf` database.
Check that InfluxDB has such a database in addition to the `_internal` database.

    docker-compose run influxdb-cli
    > show databases

### Check that Chronograf works

Access the Chronograf inteface, [http://localhost:8888](http://localhost:8888)

### Check Kapacitor works

First, run this curl command, if no errors occur Kapacitor is running:

    curl http://localhost:9092/kapacitor/v1/ping


Use the built-in kapacitor cli service:

    docker-compose run kapacitor-cli
    $ kapacitor list tasks

Confirm Kapacitor is subscribed to all databases in InfluxDB

    docker-compose run influxdb-cli
    > show subscriptions

## Supported Docker versions

This image is officially supported on Docker version 1.10.1 or newer.

## Import Data

See this blog post on [importing data](https://www.influxdata.com/blog/getting-started-writing-data-to-influxdb/).

1. Download a dataset
   ```
   curl -O https://raw.githubusercontent.com/Anaisdg/GettingStarted_WritingPoints/master/Data/import.txt
   ```
2. Import the dataset into influxdb 
   ```
   docker-compose run -v $(pwd)/import.txt:/data/import.txt  --rm  influxdb bash
   > influx -import -path=/data/import.txt -host=influxdb -precision=ns
   ```
3. Log into Chronograf at [http://localhost:8888/](http://localhost:8888/) and query the data with the following query
   ```SELECT mean("high") AS "mean_high" FROM "import"."autogen"."price" WHERE time > now() - 365d GROUP BY time(10s) limit 100```
   