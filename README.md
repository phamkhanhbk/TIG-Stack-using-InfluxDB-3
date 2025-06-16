# TIG Stack using InfluxDB 3 Core/Enterprise

TIG Stack is an arconym for the following three open source technologoies that seamless work togther to collect, store, analyze and monitor real time data from almost anything such as servers, APIs, IoT devices or even your smart coffee machine!

1. **T**elegraf to collect system metrics
2. **I**nfluxDB 3 (Core or Enterprise version) as the timeseries database
3. **G**rafana as data visualization in dashboard

![TIG Stack](https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3-Core/blob/main/TIG.drawio-4.png)

## Pre-requisite:

1. Docker should be installed on your local machine. We will rely on docker-compose.yaml file to pull docker images for Telegraf, InfluxDB 3 Core/Enteprise and Grafana.
2. Git
3. Editor like VS Code

# Steps:

## 1. Clone the repository
```sh
git clone https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3.git
cd TIG-Stack-using-InfluxDB-3
```

## 2. Start InfluxDB 3 & Generate Token

```sh
docker-compose up -d influxdb3-core
docker-compose exec influxdb3-core influxdb3 create token --admin
```

## 3. Start the remaining TIG Stack in Docker

```sh
docker-compose up -d
```

## 4. Verify the Stack

Check Telegraf Logs
```sh
docker-compose logs telegraf
```
Check InfluxDB 3 Logs & See Telegraf generated Tables

```sh
docker-compose logs influxdb3-core
docker-compose exec influxdb3-core influxdb3 query "SHOW TABLES" --database local_system --token YOUR_TOKEN_STRING
```

## 5. Setup & View Grafana Dashboard

- Open localhost:3000 from your browser 
- Login with credentials from .env (default: admin/admin)
- Add Data Source : 
  - Type: InfluxDB
  - Language : SQL
  - URL: http://influxdb3-core:8181
  - Token: Paste the token string you created using influxdb3 cli, which should also be in your .env file
- Add Data Visualization : Dashboards > Create Dashboard - Add Visualization > Select Data Source > InfluxDB_3_Core 
- In the query 'builder' paste and run the following SQL query to see the visualization of the data collected via Telegraf, written to InfluxDB 3.
```sql
SELECT "cpu", "usage_user", "time" FROM "cpu" WHERE "time" >= $__timeFrom AND "time" <= $__timeTo AND "cpu" = 'cpu0'
```

## 7. Stopping the TIG Stack & Removing Data

### Stop Services
```sh
docker-compose down
```
### Stop and Remove Volumes (Destroys All Data)
```sh
docker-compose down -v
```

### References

1. (TIG Stack with InfluxDB Core but without Docker tutorial)(https://www.influxdata.com/blog/tig-stack-guide-influxdb-core/)
2. [Telegraf](https://www.influxdata.com/time-series-platform/telegraf)
3. InfluxDB 3 Core(https://www.influxdata.com/products/influxdb/)
4. [Grafana](https://github.com/grafana/grafana)
