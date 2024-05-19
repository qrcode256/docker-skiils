### Short info

Multi-stage build allow us to build smaller final container image by specifying multiple helper imagers (stages) within the same **Dockerfile** . Each stage is used to complete a task. For example, building the project.
[Official docker mutli-stage build](https://docs.docker.com/build/building/multi-stage/)

It allows us to provide multiple FROM instructions to a Dockerfile. Each of these FROM instructions can COPY artifacts from the previous build stage. So, it removes all the intermediate steps such as downloading the code, installing dependencies, testing, building...etc from the final image. which will reduce the additional layers from the final image and eventually the size of the final image will be smaller and also reduces the attack surface. [source](https://sachithsiriwardana.medium.com/dockerizing-nodejs-application-with-multi-stage-build-e30477ca572)

### When to Use Multi-stage Builds
- **Reducing Image Size** When your application’s build process involves compiling code or including build-time dependencies that aren’t needed at runtime.
- **Enhancing Security** To minimize the attack surface to image and container - excluding tools and files not required for the application to run.
- **Separating Build Environments** Allow to build and run applications in a specific environment.

#### Basic example
```dockerfile
# sh Dockerfile.build 
FROM node:17.9.0
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run lint && npm run build
```

```dockerfile
# .Dockerfile.prod
FROM node:17.9.0-alpine3.15
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY distapp ./
EXPOSE 3000
ENTRYPOINT [ "node","./app.js" ]
```

```sh
# usage dockerbuild file in console
#!/bin/bash

echo building image node_project:build

docker build -t node_project:build -f Dockerfile.build .
# create a new container from the image and copy dist folder content to distapp folder
docker create --name copyContainer node_project:build
docker cp copyContainer:/usr/src/app/dist ./distapp
docker rm -f copyContainer

echo building image node_project:prod 

docker build -t node_project:prod -f Dockerfile.prod .
```

Explanation:
After start script:

1. It will build a new image from `Dockerfile.build` file.
2. Then it will create a new container from the previous image and copy the dist folder to local storage as `distapp` and remove the container.
3. In the last step, it will build the final image from `Dockerfile.prod` file. The Dockerfile will start from the node alpine base image and install only the required dependencies. After that Dockerfile will copy `distapp` folder.


To make it more short - with one commend to run

```dockerfile
# .Dockerfile

# for base
FROM node:17.9.0 AS base # AS - used to provide an alias to the stage that starts with
WORKDIR /usr/src/app
COPY package*.json ./   
RUN npm install
COPY . .

# for lint
FROM base as linter
WORKDIR /usr/src/app
RUN npm run lint

# for build
FROM linter as builder
WORKDIR /usr/src/app
RUN npm run build


# for production
FROM node:17.9.0-alpine3.15
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY --from=builder /usr/src/app/dist ./  # --from=builder - used as an option to copy files from the specified stage
EXPOSE 3000
ENTRYPOINT ["node","./app.js"]
```

Run the prev dockerfile
```sh
docker build --target base -t project:base . # --target — This is used as an option in docker build to build each stage individually
```

#### Example 2:

```dockerfile
# .Dockerfile
FROM node:20-slim AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable

FROM base AS build
COPY . /usr/src/app
WORKDIR /usr/src/app
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile
RUN pnpm run -r build
RUN pnpm deploy --filter=app1 --prod /prod/app1
RUN pnpm deploy --filter=app2 --prod /prod/app2

FROM base AS app1
COPY --from=build /prod/app1 /prod/app1
WORKDIR /prod/app1
EXPOSE 8000
CMD [ "pnpm", "start" ]

FROM base AS app2
COPY --from=build /prod/app2 /prod/app2
WORKDIR /prod/app2
EXPOSE 8001
CMD [ "pnpm", "start" ]
```
```dockerfile
# to create image run the next command

docker build . --target app1 --tag app1:latest
docker build . --target app2 --tag app2:latest
```

```dockerfile
# Multy stage image in docker

# .Dockerfile
FROM node:20-slim AS base
ENV PNPM_HOME="/pnpm"
ENV PATH="$PNPM_HOME:$PATH"
RUN corepack enable

FROM base AS build
COPY . /usr/src/app
WORKDIR /usr/src/app
RUN --mount=type=cache,id=pnpm,target=/pnpm/store pnpm install --frozen-lockfile
RUN pnpm run -r build
RUN pnpm deploy --filter=app1 --prod /prod/app1
RUN pnpm deploy --filter=app2 --prod /prod/app2

FROM base AS app1
COPY --from=build /prod/app1 /prod/app1
WORKDIR /prod/app1
EXPOSE 8000
CMD [ "pnpm", "start" ]

FROM base AS app2
COPY --from=build /prod/app2 /prod/app2
WORKDIR /prod/app2
EXPOSE 8001
CMD [ "pnpm", "start" ]

# To use this dockerfile run one of the next commands for app1 and app2:
# $ docker build . --target app1 --tag app1:latest
# $ docker build . --target app2 --tag app2:latest

```


```dockerfile
#builder image
FROM node:18 as builder
WORKDIR /build
COPY package*.json
RUN npm install

COPY src/ src/
COPY tsconfig.json tsconfig.json
RUN npm run build

# runner image
FROM node:alphine as runner
WORKDIR /app
COPY --from-builder build/package*.json .
COPY --from-builder build/dist dist/
RUN npm install  --only=prod
CMD ["npm", "start"]

```


```dockerfile
# .Dockerfile
# build stage
FROM node:12-stretch
WORKDIR /build
COPY package-lock.json package.json ./
RUN npm ci
COPY . .

# runtime stage
FROM alpine:3.10
RUN apk add --update nodejs
RUN addgroup -S node && adduser -S node -G node
USER node
RUN mkdir /home/node/code
WORKDIR /home/node/code
COPY --from=0 --chown=node:node /build . #if the image has no alias we can use an index --from=0, --from=1, ...
CMD ["node", "index.js"]

# $  docker build -t multi-node -f multi-node.Dockerfile .
# $  docker run -p 3000:3000 multi-node
```


```dockerfile

# .Dockerfile
FROM node as builder
WORKDIR /root/
COPY ["package.json", "package-lock.json", "./"]
RUN ["npm", "install"]
COPY ["webpack.config.js", "./"]
COPY ["src/", "./src/"]
RUN ["npm", "run", "build"]
RUN ["/bin/bash", "-c", "find . ! -name dist ! -name node_modules -maxdepth 1 -mindepth 1 -exec rm -rf {} \\;"]

FROM node
WORKDIR /root/
COPY --from=builder /root/ ./
ENTRYPOINT ["node", "/root/node_modules/.bin/http-server" , "./dist/"]
EXPOSE 8080

# Run the next command to build image
# $ docker build -t demo-frontend:multi-stage . 

```

Multi-stage build is a great feature, as it allows you to **keep images small and use the build cache at the same time**. But this also means **a lot of intermediate images are going to be generated**.


These intermediate images are a type of dangling image, which are images that does not have a name. Generally, you should keep these dangling images, as they are the basis of the build cache. But having them littered in the terminal can be annoying; or if you are maintaining a CI/CD server, you may also want to clean up dangling images regularly.

You can output a list of dangling images by using the *--filter* flag to the standard command:
```
docker images --filter dangling=true
REPOSITORY  TAG     IMAGE ID      SIZE
<none>      <none>  8874c0fec4c9  939MB
```

To remove dangling images, run:
```
docker rmi $(docker images --filter dangling=true --quiet)
```


#### Using LABEL instruction
The LABEL instruction allows you to specify the metadata in your image as key-value pairs. You can use labels to:

document contact details of the author and/or maintainer of the image
check the build date of the image
add licensing information

```dockerfile
# .Dockerfile

FROM node as builder
LABEL name=demo-frontend
LABEL intermediate=true
WORKDIR /root/
COPY ["package.json", "package-lock.json", "./"]
RUN ["npm", "install"]
COPY ["webpack.config.js", "./"]
COPY ["src/", "./src/"]
RUN ["npm", "run", "build"]
RUN ["/bin/bash", "-c", "find . ! -name dist ! -name node_modules -maxdepth 1 -mindepth 1 -exec rm -rf {} \\;"]

FROM node:alpine
LABEL name=demo-frontend
WORKDIR /root/
COPY --from=builder /root/ ./
ENTRYPOINT ["node", "/root/node_modules/.bin/http-server" , "./dist/"]
EXPOSE 8080

# $ docker build -t demo-frontend:labels .
# $ docker images --filter label=name=demo-frontend
#
# REPOSITORY     TAG     IMAGE ID      SIZE
# demo-frontend  labels  6965537afe54  102MB
# <none>         <none>  0cbce2a3844b  939MB

# To remove image
# docker rmi $(docker images --filter label=name=demo-frontend --filter label=intermediate=true --quiet)

```


```dockerfile
# .Dockerfile
# "bare" base image with just our source files
# which only has Node runtime - not even NPM!
FROM mhart/alpine-node:base-6 as BASE
WORKDIR /app
COPY . .

# test image installs development dependencies
# and runs testing commands
# derived from Node image that _includes_ NPM
FROM mhart/alpine-node:6 as TEST
WORKDIR /app
# Copy files _from_ BASE
# To avoid accidentally creating different
# testing environment from production
COPY --from=BASE /app .
RUN npm install
RUN npm run lint
RUN npm run ci

# final production image
FROM BASE as PROD
EXPOSE 1337
CMD ["node", "index.js"]

# The build command is the same
# $ docker build -t gleb/hello-world:bare -f Dockerfile-bare .
```


```dockerfile
# .Dockerfile
FROM node:18-alpine3.15 as ts-compiler
WORKDIR /usr/app
COPY package*.json ./
COPY tsconfig.json ./
RUN npm install --include=dev
COPY src src
# Build the project (TS to JS conversion)
RUN ["npm", "run", "build"]

FROM node:18-alpine3.15 as ts-remover
WORKDIR /usr/app
# We need package.json again
COPY --from=ts-compiler /usr/app/package*.json ./
# Move built codes from last stage here
COPY --from=ts-compiler /usr/app/build ./
# We don't need dev dependencies anymore
RUN npm install --omit=dev

# Using google optimized containers can make it even smaller
FROM gcr.io/distroless/nodejs:18
WORKDIR /usr/app
COPY --from=ts-remover /usr/app ./
USER 1000
CMD ["index.js"]
```

**For env we should repeat every time for each stage image**
```dockerfile

#.Dockerfile

FROM node:18-alpine3.15 as ts-compiler
WORKDIR /usr/app
COPY package.json .

# Define ENV once here
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true
RUN npm install --include=dev
COPY tsconfig.json .
COPY src src
RUN ["npm", "run", "build"]

FROM node:18-alpine3.15 as ts-remover
WORKDIR /usr/app
COPY --from=ts-compiler /usr/app/package.json .
COPY --from=ts-compiler /usr/app/build .

# Define ENV once again!
ENV PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=true # need to repeat the env because docker use ENV and ARG commands per Stage,
RUN npm install --omit=dev

# We don't need that ENV in this stage, 
# because we're not installing anything more.
FROM gcr.io/distroless/nodejs:18
WORKDIR /usr/app
COPY --from=ts-remover /usr/app ./
USER 1000
CMD ["index.js"]
```



```dockerfile
FROM golang:1.18-alpine3.15 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . ./
RUN CGO_ENABLED=0 GOOS=linux go build -a -o main .

# This stage convert svelte project to vanilla HTML, CSS, JS.
FROM node:18-alpine3.15 AS frontend
WORKDIR /ui
COPY ui .
RUN npm install
RUN npm run build

FROM alpine:3.15 AS production
COPY --from=builder /app/main .
COPY --from=frontend /ui/public /ui
CMD ["./main"]
```


```dockerfile
FROM node:10-alpine
RUN mkdir -p /home/node/app/node_modules && chown -R node:node /home/node/app
WORKDIR /home/node/app
COPY package*.json ./
USER node
RUN npm install
COPY --chown=node:node . .
EXPOSE 8080
CMD [ "node", "app.js" ]

# add  .dockerignore
#node_modules
#npm-debug.log
#Dockerfile
#.dockerignore

```

```dockerfile
# stage 1 (production base)
# This gets our proddependencies installed and  out of the way

FROM node:10-alphine as base
EXPOSE 3000
ENV NODE_ENV=production
WORKDIR /opt
COPY package*.json /

# We use npm ci here so only the package-lock.file
RUN npm config list \
    && npm ci \
    && npm cache clean --force

# Stage 2 (development)
# we don't copy in this stage because for dev you'll bind-mount anyway
# this save time when building locally for dev via docker-compose
FROM base as dev
ENV NODE_ENV=development
ENV PATH=/opt/node_modules/.bin:$PATH
WORKDIR /opt
RUN npm install --only=deevlopment
WORKDIR /opt/app
CMD ["nodemon", "./bin/www", "--inspect=0.0.0.0:9229"]

# Stage 3 (copy in source)
# This gets our source code into builder for use in next two stages
# It gets its own stage so we don't have to copy twice
# this stage starts from the first one and skips the last two

FROM base as source
WORKDIR /opt/app
COPY . .

# Stage 4 (testing)
# use this  in automated CI
# it has production and development dependencies
# In Build kit, it will be skipped
FROM source as test
ENV NODE_ENV=development
ENV PATH=/opt/node_modules/.bin:$path

# this copies all devepndencies (prod+dev)
COPY --from=dev /opt/node_modules /opt/node_modules

# run linters as part of build
# check if it installed with dev dependencies
RUN eslint .

#run unit test as part of build
RUN npm test

CMD ["npm", "run", "test"]

# Stage 5 (security scanning and audit)
FROM test AS audit
RUN npm audit

# aqua microscanner, which needs a token for API access
# note this isn't super secret, so we will use an arg here
# http://github.com/aquasecurity/microscanner
# https://github.com/aquasecurity/trivy
ARG MICROSCANNER_TOKEN
# download source to scan
ADD https://get.aquasec.com/microscanner / 
# set to be executable
RUN chmod +x /microscanner
# update local certificates to run correctly
RUN apk add --no-cache ca-certificates && update-ca-certificates
RUN /microscanner $MICROSCANNER_TOKEN --continue-on-failure

# Stage 6 (default, production)
# this will run be default if you don't include target
# it has prod-only dependencies
# In BulkKit, this is skipped for dev and test stages
FROM source as prod
CMD ["node", "./bin/www"]

# to run correctly
# $ docker build -t auditnode --target=audit --build-arg MICROSCANNER_TOKEN=$MICROSCANNER .
```