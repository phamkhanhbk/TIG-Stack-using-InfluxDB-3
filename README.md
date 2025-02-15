# TIG Stack using InfluxDB 3 Core

TIG Stack is an arconym for the following three open source technologoies that seamless work togther to collect, store, analyze and monitor real time data from almost anything such as servers, APIs, IoT devices or even your smart coffee machine!

1. **T**elegraf to collect system metrics
2. **I**nfluxDB 3 Core (alpha release) as database for metrics
3. **G**rafana as data visualization in dashboard

![TIG Stack](https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3-Core/blob/main/TIG.drawio-4.png)


## Pre-requisite:

1. Docker should be installed on your local machine and make sure it's running.
2. Git (optional) to clone this Git Repository

# Steps:

## 1. Clone the repository
```
git clone https://github.com/InfluxCommunity/TIG-Stack-using-InfluxDB-3-Core.git
cd TIG-Stack-using-InfluxDB-3-Core
```

## 2. Start the Stack
Make sure Docker is up and running by typing `docker info`. Once you verify it is then let's build our stack in background which is configured in the `docker-compose.yaml`. Feel free to edit it file as per your need.
```
docker-compose up -d
```

## 3. Verify metrics are being collected by Telegraf
```
docker-compose logs telegraf
```

## 4. Create InfluxDB Token & Update the .env file
```
docker-compose exec influxdb influxdb3 create token
```
### Add to the .env file the bearer token
```
INFLUXDB_TOKEN=your_new_token_here
```

## 5. Verify InfluxDB is setup correctly and tables are being created
```
docker-compose logs influxdb
docker ps
docker exec <container-id> influxdb3 query --database local_system "SHOW TABLES"
```

## 6. Setup & View Grafana Dashboard

- Open localhost:3000 from your browser 
- Login with credentials from .env (default: admin/admin)
- Add Data Source : InfluxDB at http://influxdb:8181
- Add Data Visualization : Dashboards > Create Dashboard - Add Visualization > Select Data Source > InfluxDB_3_Core 
- In the query 'builder' paste and run the following SQL
```
SELECT "cpu", "usage_user", "time" FROM "cpu" WHERE "time" >= $__timeFrom AND "time" <= $__timeTo AND "cpu" = 'cpu0'
```

## 7. Stopping the TIG Stack & Removing Data

### Stop Services
```
docker-compose down
```
### Stop Services (careful!)
```
docker-compose down -v
```

### References

1. (TIG Stack with InfluxDB Core but without Docker tutorial)(https://www.influxdata.com/blog/tig-stack-guide-influxdb-core/)
2. [Telegraf](https://www.influxdata.com/time-series-platform/telegraf)
3. InfluxDB 3 Core(https://www.influxdata.com/products/influxdb/)
4. [Grafana](https://github.com/grafana/grafana)
