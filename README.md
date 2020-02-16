# monitoring-box

Runs a suite of services for a full-fledged monitoring using Compose. Uses a bunch of a community-built Docker images.

## Services

- [Nginx proxy](https://hub.docker.com/r/jwilder/nginx-proxy/dockerfile)
- [Grafana](https://hub.docker.com/r/grafana/grafana/)
- [InfluxDB](https://hub.docker.com/_/influxdb)
- [Telegraf](https://hub.docker.com/_/telegraf)

## Getting started

### Prerequisite and dependencies

Install all dependencies list below on the server:

- [Nginx](https://www.digitalocean.com/community/tutorials/how-to-install-nginx-on-ubuntu-18-04-quickstart)
- [Certbot](https://certbot.eff.org)
- [Docker & Docker Compose](https://www.digitalocean.com/community/tutorials/comment-installer-et-utiliser-docker-sur-ubuntu-18-04-fr)
- [GIT](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-18-04-quickstart)

### Clone the repository

Clone the repository on the server in the `home` directory.

### Grafana provisioning

Grafana provisioning is enabled for datasource, dashboards and alert notifications with the `./config/grafana/` directory. On up, Grafana will mount datasource, dashboards and alert notifications from this folder.

> More informations is available on the Grafana [provisionning documentation](https://grafana.com/docs/grafana/latest/administration/provisioning/)

#### Dashboard

The project includes a dashboard example from [Grafana labs](https://grafana.com/grafana/dashboards/928).

#### Notifiers

Replace `<SLACK_WEB_HOOK>` by your personnal [Slack webhook](https://api.slack.com/messaging/webhooks#getting_started) in the `./config/grafana/notifiers.yml` file.

### Change permissions

Change permissions of `/grafana` directory to add the secific user ID `472` as the <a href="https://grafana.com/docs/installation/docker/#user-id-changes" title="Grafana documentation">Grafana installation documentation</a>.

```
chown 472:472 grafana
```

Change permissions of `/grafana` directory to authorize Grafana to write in the folder.

```
# drwxrwxr-x
sudo chmod 775 -R grafana/
```

### Edit the .env file

By default `.env` file is not versioned, run the following command to duplicate `.env.dist` and fill the file:

```
cp .env.dist .env
```

Update corresponding variables:

- `RESTART_MODE` - Docker restart mode
- `TIME_ZONE` - Time zone of containers
- `HOST_NAME` - Domain attach to the server

Like the following example:

```
RESTART_MODE=unless-stopped
TIME_ZONE=Europe/Paris
HOST_NAME=domain.com
```

## InfluxDB commands

Telegraf need an InfluxDB database with a specific retention policy.

Start InfluxDB CLI:

```
cd ./monitoring-box
docker exec -it monitoring-influxdb influx
```

Create the InfluxDB database:

```
CREATE DATABASE monitoring_influx_db
```

Create the retention `oneweek` use by Telegraf and InfluxDB:

```
CREATE RETENTION POLICY "oneweek" ON "monitoring_influx_db" DURATION 1w REPLICATION 1 DEFAULT
```

Check retentions

```
SHOW RETENTION POLICIES ON monitoring_influx_db
```

## Start the Docker Compose 🚀

Simply start all the services with the command below, which uses the `docker-compose.yml` file by default.

```bash
# -d option let you run containers in the background
docker-compose up -d
```

## Licence 🤞

`monitoring-box` is licensed under the [MIT License](http://opensource.org/licenses/MIT).

Created with ♥ by [@yoriiis](http://github.com/yoriiis).
