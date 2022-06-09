# elk-apm

Based on [deviantony/docker-elk](https://github.com/deviantony/docker-elk) repo

this docker compose file will start a ***single node*** `elasticsearch`, `logstash`, `kibana` and `apm-server` stack.

## Before running

Modify [.env](.env) file and set three passwords, and remember, do not change the password of user `elastic`, `kibana_system`, `logstash_internal` in kibana.

Modify [.env](.env) file and set `ROOT_DIR` variable to the absolute path of this directory to avoid some problems.

Modify [.env](.env) file and set `ELASTIC_VERSION` variable to the elastic stack version you want to use.

## About network

The docker compose files in this repository are assumed that you are using `nginx-proxy-manager` to manage your network traffic. And a docker network called `npm-net` has been created before you run any docker compose files in this repo.

If you do not want to use this approach, you can modify the docker compose files.

For example, if you want to create a docker network called `elk-net` when running this docker compose file and use it to run elastic stack, you can modify the docker compose files as follows:

In the end of this file, change `networks` settings:
``` yaml
# Before
networks:
  npm-net:
    external: true
# After
networks:
  elk-net:
```

In every service configuration, change `networks` settings:
``` yaml
# Before
networks:
  - npm-net
# After
networks:
  - elk-net
```

## About ports

By default, these ports on host machine will be used:
- `9200` -> elastic search
- `9300` -> elastic search
- `5044:5044` -> logstash
- `5000:tcp` -> logstash
- `5000:udp` -> logstash
- `9600` -> logstash
- `5601` -> kibana
- `8200` -> apm-server

## About configuration

You can change the configuration of any service by modifying there configuration files in `config` directory.

You can change the JVM options in docker compose file.

Default JVM options:
- Elasticsearch: `-Xms1024m -Xmx1024m`
- Logstash: `-Xms256m -Xmx256m`

By default, elasticsearch will disable any premium features by setting `xpack.license.self_generated.type` to `basic`, you can change this setting in `elasticsearch.yml` file.

## What will be done

- Pull 4 images from elastic docker registry
  - `elasticsearch`
  - `kibana`
  - `logstash`
  - `apm-server`
- Create two docker volumes
  - `elasticsearch-setup` for setup container data
  - `elasticsearch-data` for elasticsearch data

## Running 

```
docker-compose up -d
```

It will start a container named `elasticsearch-setup` first. Then bring up `elasticsearch`, `kibana`, `logstash` and `apm-server` containers. The `elasticsearch-setup` will exit with `0` after the initializing process is completed.

## After running

Access kibana on the host machine from `http://localhost:{EXPOSED_PORT}`, the default port is `5601`, login with `elastic` user and password.

You need to add `Elastic APM` intergration on your kibana dashboard.

You can use `http://apm-server:8200` to visit th APM server in the stack. Or use `http://localhost:{EXPOSED_PORT}` to visit the APM server on the host machine. Or use `http://{IP_ADDRESS_OR_DOMAIN}:{EXPOSED_PORT}` to visit the APM server from other machines.

## Upgrade

> Note: Before you upgrade the elastic stack, you should check the official documentation of upgrade first.

Run `docker-compose down` to stop all containers.

Then modify [.env](.env) file and set `ELASTIC_VERSION` variable to the elastic stack version you want to use.

Run `docker-compose pull` and `docker-compose build` to update the images.

Run `docker-compose up -d` to start all containers.
