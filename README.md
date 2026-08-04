# BIRD Docker

Minimal (~5 MB) `scratch`-based Docker image for [BIRD Internet Routing Daemon](https://bird.nic.cz/) with glibc.

## Run

```sh
docker compose up --build
```

Check its status:

```sh
docker compose exec bird birdc show status
```

## Configure

Edit [`config/bird.conf`](config/bird.conf), which Compose mounts at `/etc/bird/bird.conf`, then reload the configuration:

```sh
docker compose exec bird birdc configure
```

or recreate the container:

```sh
docker compose up --force-recreate
```

## Build and push to a registry

An example script to build the image and push it to a registry

```sh
./scripts/registry-push.sh
```
