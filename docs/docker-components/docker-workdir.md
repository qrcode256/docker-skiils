The **WORKDIR** instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile. If the WORKDIR doesn’t exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.

You should use WORKDIR instead of proliferating instructions like RUN cd … && do-something, which are hard to read, troubleshoot, and maintain.

You can rewrite your dockerfile from:

```dockerfile
FROM node:latest
RUN mkdir -p /usr/src/app
WORKDIR /usr/src/app
COPY package.json /usr/src/app/
RUN npm install
COPY . /usr/src/app
EXPOSE 3000
CMD [ “npm”, “start” ] 
```
to this one:
```dockerfile
FROM node:latest
WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . ./
EXPOSE 3000
CMD [ "npm", "start" ] 
```