## Best Practice with Security rules:

- #### Keep Host and Docker up to date.
To protect against known container escape vulnerabilities like [Leaky Vessels](https://snyk.io/blog/cve-2024-21626-runc-process-cwd-container-breakout/), which typically result in the attacker gaining root access to the host, it's vital to keep both the host and Docker up to date. This includes regularly updating the host kernel as well as the Docker Engine.


- #### Choose the right Node.js Docker base image for your application
When creating a Docker image for a Node.js project, we build our own application image based on another Docker image, which we pull from Docker Hub. This is what we refer to as the base image. The base image is the building block of the new Docker image you are about to build for your Node.js application.


The selection of a base image is critical because it significantly impacts everything from the Docker image build speed, as well as the security and performance of your web application. This is so critical Docker and Snyk co-wrote this practical guide focused on container image security for developer teams [link](https://snyk.io/learn/container-security/?utm_medium=Partner&utm_source=Snyk&utm_content=Docker-for-Nodejs-blog-Q1). 


- #### Do not expose the Docker daemon socket (even to the containers)
Docker socket /var/run/docker.sock is the UNIX socket that Docker is listening to. This is the primary entry point for the Docker API. The owner of this socket is root. Giving someone access to it is equivalent to giving unrestricted root access to your host.

If you are running docker daemon with -H tcp://0.0.0.0:XXX or similar you are exposing un-encrypted and unauthenticated direct access to the Docker daemon, if the host is internet connected this means the docker daemon on your computer can be used by anyone from the public internet. If you really, really have to do this, you should secure it. 

- #### Set a user. Don’t run containers as root
Configuring the container to use an unprivileged user is the best way to prevent privilege escalation attacks
Example 1:
```sh
docker run -u 4000 alpine
```
Example 2:
```dockerfile
FROM alpine
RUN groupadd -r myuser && useradd -r -g myuser myuser
#    <HERE DO WHAT YOU HAVE TO DO AS A ROOT USER LIKE INSTALLING PACKAGES ETC.>
USER myuser
```

The principle of least privilege is a security principle from the early days of Unix and we should always follow this when we’re running our containerized Node.js web applications.

The threat assessment is pretty straight-forward—if an attacker is able to compromise the web application in a way that allows for [command injection](https://www.snyk.io/blog/command-injection/) or [directory path traversal](https://snyk.io/blog/snyking-in-directory-traversal-vulnerability-exploit-in-the-st-package/), then these will be invoked with the user who owns the application process. If that process happens to be root then they can do virtually everything within the container, including [attempting a container escape](https://www.snyk.io/blog/a-serious-security-flaw-in-runc-can-result-in-root-privilege-escalation-in-docker-and-kubernetes/) or privilege escalation. Why would we want to risk it? You’re right, we don’t.

```sh .Dockerfile
FROM node:16.17.0-bullseye-slim
ENV NODE_ENV production
WORKDIR /usr/src/app
COPY --chown=node:node . /usr/src/app
RUN npm ci --only=production
USER node
CMD "npm" "start"
```

- #### Prevent in-container privilege escalation
Always run your docker images with --security-opt=no-new-privileges in order to prevent privilege escalation. This will prevent the container from gaining new privileges via setuid or setgid binaries.

Inter-Container Connectivity (icc) is enabled by default, allowing all containers to communicate with each other through the docker0 bridged network. Instead of using the --icc=false flag with the Docker daemon, which completely disables inter-container communication, consider defining specific network configurations.

- #### Limit resources (memory, CPU, file descriptors, processes, restarts)

The best way to avoid DoS attacks is by limiting resources. You can limit memory, CPU, maximum number of restarts (--restart=on-failure:<number_of_restarts>), maximum number of file descriptors (--ulimit nofile=<number>) and maximum number of processes (--ulimit nproc=<number>).

- #### Set filesystem and volumes to read-only

Run containers with a read-only filesystem using --read-only flag. 
Example 1: `docker run --read-only alpine sh -c 'echo "whatever" > /tmp'`
Example 2: `docker run --read-only --tmpfs /tmp alpine sh -c 'echo "whatever" > /tmp/file'` - if we need to save temporary


- #### Integrate container scanning tools into your CI/CD pipeline


- #### Keep the Docker daemon logging level at info
By default, the Docker daemon is configured to have a base logging level of info. This can be verified by checking the daemon configuration file /etc/docker/daemon.json for thelog-level key. If the key is not present, the default logging level is info. Additionally, if the docker daemon is started with the --log-level option, the value of the log-level key in the configuration file will be overridden. 
`ps aux | grep '[d]ockerd.*--log-level' | awk '{for(i=1;i<=NF;i++) if ($i ~ /--log-level/) print $i}'`


- ####  Run Docker in rootless mode
Rootless mode ensures that the Docker daemon and containers are running as an unprivileged user, which means that even if an attacker breaks out of the container, they will not have root privileges on the host, which in turn substantially limits the attack surface. This is different to userns-remap mode, where the daemon still operates with root privileges.

- #### Utilize Docker Secrets for Sensitive Data Management

Docker Secrets provide a secure way to store and manage sensitive data such as passwords, tokens, and SSH keys. Using Docker Secrets helps in avoiding the exposure of sensitive data in container images or in runtime commands.

`docker secret create my_secret /path/to/super-secret-data.txt`
`docker service create --name web --secret my_secret nginx:latest`

Or in Docker Compose:

```docker-compose  Docker Compose:

 version: "3.8"
  secrets:
    my_secret:
      file: ./super-secret-data.txt
  services:
    web:
      image: nginx:latest
      secrets:
        - my_secret
```


- #### Scan your Node.js Docker image during development

Creating a Docker image based on other images, as well as rebuilding them can potentially introduce new vulnerabilities, but there’s a way for you to be on top of it.

These tests include static file checks—also known as linters—to ensure you’re avoiding security pitfalls and other bad patterns in your Dockerfile. We’ve outlined some of these in our [Docker image security best practices](https://snyk.co/DockerImageSecurityTips). If you’re a Node.js application developer you’ll also want to read through this step-by-step [10 best practices to containerize Node.js web applications with Docker](https://snyk.co/SecureNodejsImages).

Test image with snyk
```sh
$ npm install -g snyk # Install the Snyk CLI
$ snyk auth # Then let the Snyk CLI automatically grab an API token for you with
$ snyk container test nodejs:notification-v99.9 # Scan the local base image
```

[What Snyk Container detects](https://docs.snyk.io/scan-with-snyk/snyk-container/scan-container-images):

When Snyk Container tests an image using any of the available integrations, Snyk first finds the software installed in the image, including:
- dpkg, rpm, and apk operating systems packages
- Popular unmanaged software (installed outside a package manager)
- Application packages that are based on the presence of a manifest file.

Because Snyk reads the information from the file system, the container does not need to be run. This means that for a successful scan, no container or foreign code must be run.

- ####  Monitor your deployed Docker images for your Node.js applications

If you’re practicing all of the security guidelines we covered so far with scanning and fixing base images, that’s great. However, keep in mind that new security vulnerabilities get discovered all the time. If you have 78 security vulnerabilities in your image now, that doesn’t mean you won’t have 100 tomorrow morning when new CVEs are reported and impact your running containers in production. That’s why monitoring your registry of container images—those that you’re using to deploy containers—is crucial to ensure you will find out about security issues soon and can remediate them.

- #### Follow security guidelines and production-grade recommendation for a secure and optimal Node.js Docker image


- #### Use explicit and deterministic Docker base image tags
Don't use `:latest` as it could produce extra vulnerabilities for every new image build
If we build the image from node—which effectively means the node:latest tag—then every build will pull a newly built Docker image of node. We don’t want to introduce this sort of non-deterministic behavior.

The recomend way is to use the Docker image digest, which is the static `SHA256 hash` of the image. This ensures that you are getting deterministic Docker image builds from the base image.

The recommended Node.js Docker image to use would be: `FROM node:16.17.0-bullseye-slim`

Example: 
```sh
FROM node@sha256:18ae6567b623f8c1caada3fefcc8746f8e84ad5c832abd909e129f6b13df25b4  #Using the Docker image digest ensures a deterministic image but it could be confusing or counterproductive for some image scanning tools who may not know how to interpret this. For that reason, using an explicit Node.js runtime version such as `16.17.0` is preferred. 
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN npm install
CMD "npm" "start"
```
Node.js Alpine is an unofficial Docker container image build that is maintained by the Node.js Docker team. The Node.js image bundles the Alpine operating system which is powered by the minimal busybox software tooling and the musl C library implementation. These two Node.js Alpine image characteristics contribute to the Docker image being unofficially supported by the Node.js team. Furthermore, many security vulnerabilities scanners can’t easily detect software artifacts or runtimes on Node.js Alpine images, which is counterproductive to efforts to secure your container images.


- #### Install only production dependencies in the Node.js Docker image

Example - `RUN npm install` include all dependencies in the container, including devDependencies which don't need in production

Much better to use `RUN npm ci --only=production`

```sh .Dockerfile
FROM node:16.17.0-bullseye-slim
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN npm ci --only=production
CMD "npm" "start"
```

- #### Optimize Node.js tooling for production

When you build your Node.js Docker image for production, you want to ensure that all frameworks and libraries are using the optimal settings for performance and security.

`ENV NODE_ENV production`

Some frameworks and libraries may only turn on the optimized configuration that is suited to production if that NODE_ENV=environment variable is set to production

```sh .Dockerfile
FROM node:16.17.0-bullseye-slim
ENV NODE_ENV production
WORKDIR /usr/src/app
COPY . /usr/src/app
RUN npm ci --only=production
CMD "npm" "start"
```

- #### Safely terminate Node.js Docker web applications






Articles:

- [Docker Security Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Docker_Security_Cheat_Sheet.html)
- [10 Docker Security Best Practices](https://snyk.co/DockerImageSecurityTips)
- [10 best practices to containerize Node.js web applications with Docker](https://snyk.co/SecureNodejsImages)
- [10 best practices to containerize Node.js web applications with Docker](https://snyk.io/blog/10-best-practices-to-containerize-nodejs-web-applications-with-docker/)
