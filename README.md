# TIG Stack using InfluxDB 3 Core

## 1. Install & Setup InfluxDB 3 Core (alpha release)

### Install & Start InfluxDB via shell script.

For detailed installation instructions, refer to this [guide](https://docs.influxdata.com/influxdb3/core/get-started/)

`curl -O https://www.influxdata.com/d/install_influxdb3.sh && sh install_influxdb3.sh`

Follow the prompt to install it locally (for this example recommend to use object storage as your local filesystem)

### Verify the installation 

`influxdb3 --version`

It should be running in the background now on localhost at port 8181!

### Create a token

`influxdb3 create token`

Save the token information as we will use the plain text token with Telegraf in next step. The hashed token is a cryptographic representation of the plain token.

## 2. Install & Setup Telegraf 

MacOS example below, for other operating systems please follow this [guide](https://docs.influxdata.com/telegraf/v1/install/?t=macOS#download-and-install-telegraf)

```
brew update
brew install telegraf
```

### Configure Telegraf to send system CPU metrics to InfluxDB v3 

Create a configuration file for CPU Input Plugin and the InfluxDB v2 Output Plugin in your current directory
```
touch telegraf.conf
```
Open and edit **telegraf.config** file and paste the following configration, make sure to update the _'token'_ with InfluxDB 3 token you created previously.

```
# Global configuration
[agent]
  interval = "10s"           # Collection interval
  flush_interval = "10s"     # Data flush interval
# Input Plugin: CPU Metrics
[[inputs.cpu]]
  percpu = true              # Collect per-CPU metrics
  totalcpu = true            # Collect total CPU metrics
  collect_cpu_time = false   # Do not collect CPU time metrics
  report_active = true       # Report active CPU percentage
# Output Plugin: InfluxDB v2
[[outputs.influxdb_v2]]
  urls = ["http://127.0.0.1:8181"]
  token = "<your plain Token apiv3_xxx>"
  organization = ""
  bucket = "cpu"
```

### Run Telegraf agent in the background

```sh
telegraf --config pwd/telegraf.conf --debug
```

### Test InfluxDB & Telegraf

Open a new terminal window and run the following influxdb3 commands. These will query the data using SQL to verify it's being stored in InfluxDB database named 'CPU' using Telegraf.

```sql
influxdb3 query --database=cpu "SHOW TABLES"
influxdb3 query --database=cpu "SELECT * FROM cpu LIMIT 10"
```

## 3. Install & Setup Grafana 

Install Grafana locally, following are instructions to install and run for MacOS, for other operating systems refer to this [guide](https://grafana.com/docs/grafana/latest/setup-grafana/installation)

```
brew update
brew install grafana
brew services start grafana
```

### Configure Grafana with InfluxDB 3 Core

Open localhost:3000 in your browser to configure Grafana:

1. Navigate to **Connections > Search and Select **InfluxDB** --> Add a new data source
2. Give it a name
3. Select **SQL** as the query language (default one for InfluxDB v3). 
4. Provide the following credentials in the configuration:
   - **URL**: http://localhost:8181
   - **Database**: cpu 
   - **Insecure Connection**: toggle on

5. Hit **Save & Test** to verify that you can connect to InfluxDB 3 Core.

## Create a Monitoring Dashboard

- Click 'Build a Dashboard' > 'Add a Visualization' > Select your recently created InfluDB 3 Core Data Source
- Open the Panel Code and paste the following SQL Query
  ```sql
  SELECT "cpu", "usage_user", "time" FROM "cpu" WHERE "time" >= $__timeFrom AND "time" <= $__timeTo AND "cpu" = 'cpu0'
  ```
- Run the query to see the visualization and Save the dashboard

