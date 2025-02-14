# TIG Stack using InfluxDB 3 Core

A simple microservice architecture running 3 docker contianers to power open source TIG Stack using:

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

## 2. Set Up Environment Variables
Open and Update the `.env` file in the project root using information

## 3. Start the Stack

Make sure Docker is up and running by typing `docker info`. Once you verify it is then let's build our stack in background which is configured in the `docker-compose.yaml`. Feel free to edit it file as per your need.

```
docker-compose up -d
docker-compose ps
```

# Vertify metrics are being collected by Telegraf
```
docker-compose logs telegraf
```

### Check InfluxDB 3 Core is up and running on port 8181
```
docker-compose logs influxdb
```

### Create an InfluxDB token (first time only) and update in your .env file
```
docker-compose exec influxdb influxdb3 create token
```
### Verify InfluxDB Tables by running a simple SQL Query
```
docker exec <container-id> influxdb3 query --database local_system "SHOW TABLES"
```

### View Grafana Dashboard

- Open localhost:3000 from your browser 
- Login with credentials from .env (default: admin/admin)
- Add Data Source : InfluxDB 3 Core
- Add Data Visualization : Dashboards > Create Dashboard - Add Visualization > Select Data Source > InfluxDB_3_Core 
- In the query 'builder' paste and run the following SQL
```
SELECT "cpu", "usage_user", "time" FROM "cpu" WHERE "time" >= $__timeFrom AND "time" <= $__timeTo AND "cpu" = 'cpu0'
```

## Stopping the TIG Stack & Removing Data

### Stop Services
```
docker-compose down
```
### Stop Services (careful!)
```
docker-compose down -v
```

