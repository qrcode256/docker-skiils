Cache:

```sh
RUN npm install                  # Install dependencies
RUN --mount=type=cache,target=/root/.cache,id=gocache # go build ...
```

And invoke with  docker build with params
```sh
docker build --cache-mount id=gocache,type=volume,volume=myvolume . # . If no --cache-mount is specified, the usual snapshotter-based location is used.
```

In host directory we can create *host cache*

```sh
# ensure host path
$ mkdir -p /tmp/gocache

# create volume
$ docker volume create --driver local \
      --opt type=none \
      --opt device=/tmp/gocache \   
      --opt o=bind \
      myvolume 

$ docker run -it --volume myvolume:/go/pkg/mod busybox touch /go/pkg/mod/test.txt # test.txt will be created under host dir /tmp/gocache/
```

When processing a `RUN apt-get -y` update command the files updated in the container aren't examined to determine if a cache hit exists. In that case just the command string itself is used to find a match.


Multy stage with cache

```sh
#.Dockerfile

FROM ubuntu:focal as base
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get --assume-yes install \
    nodejs npm
FROM base as builder
WORKDIR /app
COPY package*.json /app
RUN --mount=type=cache,target=/app/npm/cache \
    npm install --cache /app/npm/cache --loglevel error

FROM base as runner
# Copy node_modules
COPY --from=builder /app/node_modules /app/node_modules
# Copy your files
COPY . /app
CMD ["/bin/bash"]
```