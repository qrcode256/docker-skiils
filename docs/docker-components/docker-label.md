# https://buddy.works/tutorials/optimizing-dockerfile-for-node-js-part-2

### LABEL
The LABEL instruction allows you to specify the metadata in your image as key-value pairs. You can use labels to:

Document contact details of the author and/or maintainer of the image
- check the build date of the image
- add licensing information
- use labels to mark an image as intermediate and belonging to the demo-frontend build

```sh
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
ENTRYPOINT ["node", "/root/node_modules/.bin/http-server" , "./dist/"]
EXPOSE 8080

# $ docker build -t demo-frontend:labels .
# $ docker images --filter label=name=demo-frontend
#
# REPOSITORY     TAG     IMAGE ID      SIZE
# demo-frontend  labels  6965537afe54  102MB
# <none>         <none>  0cbce2a3844b  939MB
```

### Semantics labels

Docker recommends that all LABEL instructions should have keys that are namespaced with the reverse DNS name of the domain that you own. This will help avoid clashes in label key names. Therefore, we should update our labels accordingly:

```sh
FROM node as builder
LABEL works.buddy.name="demo-frontend" \
    works.buddy.intermediate="true" \
    pubkey-id="security@example.com" \
    pubkey-fingerprint="B3B04F8CF186436EF8F1CDAD7C6ACC9EE3A31016" \
    pubkey-url="https://pgp.mit.edu/pks/lookup?op=get&search=0xFD5EB4DB480717ED"
WORKDIR /root/
COPY ["package.json", "package-lock.json", "./"]
RUN ["npm", "install"]
COPY ["src/", "./src/"]
RUN ["npm", "run", "build"]
RUN ["/bin/bash", "-c", "find . ! -name dist ! -name node_modules -maxdepth 1 -mindepth 1 -exec rm -rf {} \\;"]
ENTRYPOINT ["node", "/root/node_modules/.bin/http-server" , "./dist/"]
EXPOSE 8080
```

- [Label shema ](http://label-schema.org/rc1/)
- [Generic labels](https://github.com/projectatomic/ContainerApplicationGenericLabels)
      