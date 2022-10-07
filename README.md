# Reproduction test

## Add Devcontainer

1. Add devcontainer
   * select ``Add Dev Container Configuration file...``
   * ``ubuntu``
   * ``22.04``
1. Setup proxy in devcontainer.json
1. The development container builds and starts without issue.

## Add sample docker-compose

Build docker-compose in the local environment with the following command.

```
docker compose -f $(pwd)/compose.yml build --build-arg http_proxy=$http_proxy --build-arg https_proxy=$https_proxy --no-cache
```

## Change devcontainer.json setting from Dockerfile to docker-compose.

Build docker-compose in the local environment with the following command.

```
docker compose --project-name $(basename $(pwd)) -f $(pwd)/compose.yml -f $(pwd)/.devcontainer/docker-compose.yml build --build-arg http_proxy=$http_proxy --build-arg https_proxy=$https_proxy --no-cache
```

```
docker compose --project-name $(basename $(pwd)) -f $(pwd)/compose.yml -f $(pwd)/.devcontainer/docker-compose.yml up -d
```

Check the operation of docker-compose with the command below.

```
curl localhost:8000
docker compose exec app curl web:5000
```

Stop and delete container.

```
docker compose --project-name $(basename $(pwd)) -f $(pwd)/compose.yml -f $(pwd)/.devcontainer/docker-compose.yml rm --stop
```

## Reopen in Container

Execute ``Reopen in Container``.
This works fine.

When I modify .devcontainer/Dockerfile and run ``Rebuild Container``, it fails.

For example

```
$ git diff .devcontainer/Dockerfile 
diff --git a/.devcontainer/Dockerfile b/.devcontainer/Dockerfile
index 828de1f..41a49cf 100644
--- a/.devcontainer/Dockerfile
+++ b/.devcontainer/Dockerfile
@@ -8,4 +8,10 @@ FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}
 # RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
 #     && apt-get -y install --no-install-recommends <your-package-list-here>
 
+RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
+    git \
+    nmap \
+ && apt-get -y autoremove && rm -rf /var/lib/apt/lists/*
```

Execute ``Rebuild Container``.
This doesn't work.

Check the failed command in the log.
I think that if you set the proxy with the build-arg option in the docker-compose build command, it will work correctly.

```
[2022-10-07T08:20:42.974Z] Start: Run: docker-compose --project-name devcon-compose -f /home/ubuntu/ghq/github.com/ksaito1125/devcon-compose/compose.yml -f /home/ubuntu/ghq/github.com/ksaito1125/devcon-compose/.devcontainer/docker-compose.yml -f /tmp/devcontainercli-ubuntu/docker-compose/docker-compose.devcontainer.build-1665130842974.yml build
[2022-10-07T08:20:43.171Z] [+] Building 0.0s (0/0)                                                         
[2022-10-07T08:20:43.298Z] [+] Building 0.0s (1/1)                                                         
...
```