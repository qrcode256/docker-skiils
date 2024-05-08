### Short info

Multi-stage build allow us to build smaller final container image by specifying multiple helper imagers (stages) within the same **Dockerfile** . Each stage is used to complete a task. For example, building the project.

It allows us to provide multiple FROM instructions to a Dockerfile. Each of these FROM instructions can COPY artifacts from the previous build stage. So, it removes all the intermediate steps such as downloading the code, installing dependencies, testing, building...etc from the final image. which will reduce the additional layers from the final image and eventually the size of the final image will be smaller and also reduces the attack surface. [source](https://sachithsiriwardana.medium.com/dockerizing-nodejs-application-with-multi-stage-build-e30477ca572)


#### Basic example
```sh Dockerfile.build 
FROM node:17.9.0
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run lint && npm run build
```

```sh .Dockerfile.prod
FROM node:17.9.0-alpine3.15
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install --only=production
COPY distapp ./
EXPOSE 3000
ENTRYPOINT [ "node","./app.js" ]
```

```sh usage dockerbuild file in console
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

```sh
# base .Dockerfile
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

```sh
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
```sh
# to create image run the next command

docker build . --target app1 --tag app1:latest
docker build . --target app2 --tag app2:latest
```


