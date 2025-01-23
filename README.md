# TIG Stack using InfluxDB 3 Core

## 1. Install & Setup InfluxDB 3 Core (alpha release)

### Start InfluxDB

`influxdb3 serve --host-id=local01 --object-store file --data-dir ~/.influxdb3`

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

Create a configuration file for CPU Input Plugin and the InfluxDB v2 Output Plugin
```
telegraf --input-filter cpu:http --output-filter influxdb_v2:file config > telegraf.conf
```
Open **telegraf.config** file and paste the following configration, make sure to update the _'token'_ with InfluxDB 3 token you created previously.

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

### Run Telegraf agent

`telegraf --config pwd/telegraf.conf --debug`

### Test InfluxDB & Telegraf

Run the following influxdb3 command to query the data o verify it's being stored in InfluxDB using Telegraf.

`influxdb3 query --dbname=cpu "SELECT * FROM cpu LIMIT 10"`


## 3. Install & Setup Grafana 

Install Grafana locally, following are instructions to install and run for MacOS, for other operating systems refer to this [guide](https://grafana.com/docs/grafana/latest/setup-grafana/installation)

```
brew update
brew install grafana
brew services start grafana
```

### Configure Grafana with InfluxDB 3 Core

Open localhost:3000 in your browser to configure Grafana:

1. Navigate to **Connections > Datasources > New** > Search and Select **InfluxDB**.
2. Select **SQL** as the language type. 
3. Provide the following credentials in the configuration:
   - **URL**: http://localhost:8181
   -  **Database**: cpu 
   - **Insecure Connection**: toggle on

4. Hit **Save & Test** to verify that you can connect to InfluxDB 3 Core. 

