
### Base Docker question
__________

1. ⭐⭐How do you create a multi-stage build in Docker?
    <details> <summary>answer </summary>
      Multi-stage builds in Docker allow you to create optimized Docker images by leveraging multiple build stages. Each stage can use a different base image and perform specific build steps. To create a multi-stage build, you define multiple FROM instructions in the Dockerfile, each representing a different build stage.
      <br/><br/>
      Intermediate build artifacts can be copied between stages using the `COPY --from instruction`. This technique helps reduce the image size by excluding build tools and dependencies from the final image.
    </details>

1. ⭐⭐ What is the purpose of Docker secrets?
    <details> <summary>answer </summary>
      Docker secrets provide a secure and convenient way to manage sensitive data, such as passwords, API keys, and certificates, within a Docker Swarm cluster. Secrets are encrypted and only made available to services that have explicit access. They help ensure that sensitive information is kept confidential and not exposed in clear text or stored in version control systems.
    </details>

1. ⭐⭐⭐ How do you securely manage secrets in a Dockerized application?
    <details> <summary>answer </summary>
      Docker provides a built-in mechanism called Docker Swarm Secrets to securely manage sensitive information like passwords, API keys, or certificates.

      Using Docker Swarm Secrets:

      Create a secret:

      ```sh 
        echo "secret" | docker secret create my_secret -
      ```

      Deploy the service with the secret:

      ```bash 
        version: '3.8'
        services:
          myapp:
            image: myapp_image:latest
          secrets: 
            - my_secret
        secrets:
          my_secret: 
            external: true
      ```

      In this example, we create a secret called my_secret and use it in the myapp service. The actual secret value is not stored in the Docker Compose file, enhancing security.
    </details>


1. ⭐ What is the purpose of Docker container orchestration?
    <details> <summary>answer </summary>
      Docker container orchestration refers to the management and coordination of multiple containers in a distributed environment. It involves automating the deployment, scaling, scheduling, and monitoring of containers to ensure high availability, load balancing, fault tolerance, and efficient resource utilization.
      
      Container orchestration platforms like Docker Swarm, Kubernetes, or HashiCorp Nomad provide tools and features to simplify the management of containerized applications at scale.
    </details>
1. ⭐ How do you monitor Docker containers?
    <details> <summary>answer </summary>
      There are various ways to monitor Docker containers, including:
      
      - Using Docker's built-in container monitoring commands, such as **docker stats** and **docker container stats**, to view resource usage statistics.
      - Integrating with container monitoring and logging tools like Prometheus, Grafana, or ELK stack (Elasticsearch, Logstash, Kibana) to collect and analyze container metrics and logs.
      - Leveraging container orchestration platforms that offer built-in monitoring capabilities such as Docker Swarm's service metrics or Kubernetes' metrics API.
      - Using specialized monitoring agents or tools that provide container-level insights and integration with broader monitoring and alerting systems.
    </details>
1. ⭐⭐ What are some best practices for using Docker in production environments?
    <details> <summary>answer </summary>
      Some best practices for using Docker in production environments include:
      <br/><br/>
      - Building and using lightweight container images to improve deployment speed and reduce the attack surface.
      - Regularly updating Docker and the underlying host system with security patches.
      - Implementing container orchestration platforms, such as Docker Swarm or Kubernetes, to manage containers at scale and provide features like load balancing and service discovery.
      - Configuring resource limits for containers to prevent resource contention and ensure fair allocation.
      - Monitoring container health, resource usage, and application metrics for performance optimization and troubleshooting.
      - Implementing secure network configurations such as using private networks and encrypting container traffic.
      - Backing up critical data and using volume or storage solutions that provide data persistence and redundancy.
      - Implementing a comprehensive security strategy, including container vulnerability scanning, access controls, and least privilege principles.
    </details>
1. ⭐⭐ How do you automate the deployment of Docker containers?
    <details> <summary>answer </summary>
      The deployment of Docker containers can be automated using tools and practices like:
      <br/><br/>
      **Docker Compose**: Define the application's services, networks, and volumes in a Compose file. Use the docker-compose command to deploy the containers with a single command.
      <br/><br/>
      **Docker Swarm or Kubernetes**: Utilize container orchestration platforms to define and deploy the application as a service or a set of pods/replicas. This allows automatic scaling, load balancing, and self-healing capabilities.
      <br/><br/>
      **Continuous integration/continuous deployment (CI/CD) pipelines**: Integrate Docker into CI/CD workflows using tools like Jenkins, GitLab CI/CD, or CircleCI to automatically build, test, and deploy Docker containers based on code changes.
      <br/><br/>
      **Infrastructure-as-code (IaC)**: Use tools like Terraform or AWS CloudFormation to define the infrastructure stack, including Docker hosts/clusters, networks, and storage, to enable automated provisioning and container deployment.
    </details>
1. ⭐⭐ What is Docker content trust?
    <details> <summary>answer </summary>
      Docker Content Trust is a security feature that uses digital signatures to verify the authenticity and integrity of Docker images. It ensures that only trusted and signed images are used in a Docker environment, preventing the execution of potentially malicious or tampered images.
      <br/><br/>
      When Docker Content Trust is enabled, Docker clients verify the authenticity of images using cryptographic keys before pulling and running them.
    </details>
1. ⭐⭐ How do you use Docker with Kubernetes?
    <details> <summary>answer </summary>
      Docker can be used as the container runtime within a Kubernetes cluster. When deploying applications on Kubernetes, Docker is responsible for creating and managing containers on each node.
      <br/><br/>
      Kubernetes uses the Docker API to interact with Docker and perform container-related operations such as pulling images, creating containers, and managing their lifecycle. Docker images are typically stored in a container registry accessible to the Kubernetes cluster.
    </details>
1. ⭐⭐ How do you configure automatic container restarts in Docker?
    <details> <summary>answer </summary>
      Automatic container restarts can be configured in Docker by using the --restart flag when running a container. The --restart flag accepts different policies such as "no" (no automatic restart), "always" (restart regardless of the exit status), "on-failure" (restart only if the container exits with a non-zero exit status), and "unless-stopped" (restart always except when explicitly stopped).
      <br/><br/>
      For example, to configure a container to automatically restart, you can use the following command:

      ```bash
        docker run --restart=always <image>
      ```
    </details>
1. ⭐ What is the role of the Docker API in container management?
    <details> <summary>answer </summary>
      The Docker API provides a programmatic interface to interact with the Docker daemon and manage containers, images, networks, and other Docker resources. It allows developers and system administrators to automate container-related tasks. These include creating and managing containers, pulling and pushing images, inspecting container status, and interacting with Docker Swarm or other orchestration platforms.
      <br/><br/>
      The Docker API is used by various tools and libraries to integrate Docker into larger systems or build custom container management solutions.
    </details>
1. ⭐ How do you share data between containers in Docker?
    <details> <summary>answer </summary>
      Docker provides several ways to share data between containers:

      - **Using Docker volumes**: Create a shared volume and mount it into multiple containers. Changes made by one container will be visible to others sharing the same volume.

      - **Sharing a directory**: Mount a host directory into multiple containers, allowing them to read and write data to the shared directory on the host machine.

      - **Utilizing network services**: Containers can communicate with each other over the network using exposed ports or internal network connections.

      - **Using shared data stores**: Containers can access shared databases, object storage systems, and other external data sources to exchange data.
    </details>

1. ⭐⭐ How do you configure health checks for Docker containers?
    <details> <summary>answer </summary>
      Health checks can be configured for Docker containers using the HEALTHCHECK instruction in the Dockerfile or by specifying health check options during container runtime. The HEALTHCHECK instruction allows you to define a command or script that periodically checks the container's health status.
      <br/><br/>
      Docker monitors the output of the health check command and updates the container's health status accordingly. Health check options can also be specified in the docker run command using flags like --health-cmd, --health-interval, and --health-retries.
      <br/><br/>
      For more details [effective-docker-healthchecks-for-node-js](https://patrickleet.medium.com/effective-docker-healthchecks-for-node-js-b11577c3e595)
    </details>

1. ⭐⭐ What is the purpose of the health check feature in Docker?
    <details> <summary>answer </summary>
      The health check feature in Docker allows you to define a command or a script that periodically checks the health of a containerized application. Docker monitors the health check and provides information about the container's health status.

      This information can be used by orchestrators like Docker Swarm or Kubernetes to make informed decisions about container lifecycle management such as restarting or rescheduling unhealthy containers. The health check feature helps ensure the availability and reliability of applications running in Docker containers.
    </details>

1. ⭐⭐ How do you configure a custom logging driver in Docker?
    <details> <summary>answer </summary>
      To configure a custom logging driver in Docker, you can follow these steps:
      
      Create a custom logging driver plugin or ensure that the desired logging driver is installed and available on the Docker host.
      Modify the Docker daemon configuration file (usually located at /etc/docker/daemon.json) to include the logging driver configuration.
      For example:
      ```javascript
        { "log-driver": "mycustomdriver", "log-opts": { "option1": "value1", "option2": "value2" } }
      ```
      
      Replace "mycustomdriver" with the name of the custom logging driver and configure any additional options specific to the driver.
      
      Restart the Docker daemon for the changes to take effect.
      Start containers with the custom logging driver using the `--log-driver` flag.
      For example:
      ```bash
          docker run --log-driver=mycustomdriver < image >
      ```
    </details>
1. ⭐⭐ What is the purpose of the "docker system prune" command?
    <details> <summary>answer </summary>
      The docker system prune command is used to clean up unused Docker resources including stopped containers, unused networks, dangling images, and unused volumes. It helps reclaim disk space and improve system performance by removing resources that are no longer needed.

      Note: The docker system prune command should be used with caution as it permanently deletes unused resources. Any data associated with them will be lost.
    </details>
1. ⭐⭐ How do you use Docker secrets in a non-Swarm environment?
    <details> <summary>answer </summary>
      Docker secrets are primarily designed for Docker Swarm environments. However, in a non-Swarm environment, you can still utilize secret management solutions provided by external tools or by your container orchestration platform.

      For example, if you are using Kubernetes, you can leverage Kubernetes Secrets to manage and inject sensitive data into containers. Alternatively, you can use environment variables, encrypted configuration files, and secure key management systems to handle sensitive information within individual containers.

      The approach will depend on the specific requirements and tools used in your non-Swarm environment.
    </details>

1. ⭐⭐⭐ What is Docker's support for GPU acceleration?
    <details> <summary>answer </summary>
      Docker provides support for GPU acceleration through the use of NVIDIA Docker. NVIDIA Docker is a toolkit that extends Docker's capabilities to work seamlessly with NVIDIA GPUs. It allows containers to access and utilize the GPU resources available on the host system, enabling GPU-accelerated computations within Docker containers.

      This is particularly useful for applications that require high-performance computing, machine learning, and deep learning tasks that can benefit from GPU processing power.
    </details> 

1. ⭐⭐⭐ How do you troubleshoot issues with Docker containers?
    <details> <summary>answer </summary>
      Troubleshooting Docker containers involves several steps:

      - Check the container's logs for any error messages or abnormal behavior using the docker logs command.
      - Inspect the container's metadata and runtime details with commands like docker inspect or docker stats.
      - Verify the container's resource allocation and constraints such as CPU and memory limits.
      - Check the host system's logs for any related issues or resource constraints.
      - If networking issues are suspected, examine the container's network configuration and connectivity.
      - Ensure that the Docker daemon and related services are running correctly.
      - Consult the Docker documentation, community forums, and relevant online resources for specific error messages or known issues.
      - If necessary, recreate or redeploy the container to rule out any configuration or state-related issues.
    </details>

1. ⭐⭐⭐ What are some common Docker security vulnerabilities and how do you mitigate them?
    <details> <summary>answer </summary>
      Common Docker security vulnerabilities and their mitigation include:

      **Insecure container images**: Mitigate by using official or trusted images from reputable sources. Regularly update images to patch vulnerabilities and scan images for known security issues.

      **Excessive container privileges**: Mitigate by running containers with the principle of least privilege. Avoid running containers as root, and leverage user namespaces or seccomp profiles to restrict container privileges.

      **Inadequate container isolation**: Mitigate by using appropriate resource constraints. Leverage Docker's built-in isolation mechanisms like namespaces and control groups, and enable container runtime security features like AppArmor or SELinux.

      **Unsecured Docker daemon**: Mitigate by securing the Docker daemon with TLS certificates. Enable access control with user authentication and authorization. Use firewall rules to limit network access to the Docker daemon.

      **Insider threats**: Mitigate by implementing strong access control policies, regularly auditing and monitoring container activities, and educating users about security best practices.

      **Network security risks**: Mitigate by securing container network communications using encryption and network segmentation. Regularly update Docker to benefit from security patches and fixes.
    </details>

1. ⭐⭐⭐ How do you monitor Docker container resource usage?
    <details> <summary>answer </summary>
      Docker provides several options for monitoring container resource usage:

      **Docker Stats**: Use the docker stats command to view real-time resource usage statistics of running containers including CPU, memory, network, and disk I/O.

      **Container-specific monitoring tools**: Utilize container monitoring tools like Prometheus, cAdvisor, or Datadog which collect and aggregate resource metrics from Docker containers for monitoring and analysis.

        Details https://docs.docker.com/engine/daemon/prometheus/

      Docker API: Query the Docker API to retrieve container resource metrics programmatically. The API provides endpoints for accessing detailed container statistics and usage information.

      Container orchestration platforms: Platforms like Docker Swarm or Kubernetes typically offer built-in container monitoring capabilities, allowing you to monitor and visualize container resource usage at scale.
    </details>

## Docker networks
_______________________________________________________

1.  ⭐ What are Docker networks?
    <details> <summary>answer </summary>
      Docker networks are virtual networks that allow containers to communicate with each other securely. They provide an isolated environment for containers and enable seamless connectivity between them.
      <br/><br/>
      Docker networks can be created using different drivers, such as bridge, overlay, and host, depending on the specific requirements of the application.
    </details>
1. ⭐ How do you create a custom Docker network?
    <details> <summary>answer </summary>
      To create a custom Docker network, you can use the docker network create command followed by the desired network name. For example:
      <br/><br/>
      docker network create mynetwork
      <br/><br/>
      This command creates a new custom network named "mynetwork" using the default bridge driver. You can also specify a different driver using the `--driver` option if needed.
    </details>
1. ⭐⭐ What is the purpose of the bridge network driver in Docker?
    <details> <summary>answer </summary>
      The bridge network driver is the most commonly used driver and is suitable for most applications. It provides a default network bridge called "bridge" that allows containers to communicate with each other on the same host. It enables containers to connect to each other and to the outside world through the host machine's network interface.
    </details>
1. ⭐⭐ What is Docker overlay networking?
    <details> <summary>answer </summary>
      Docker overlay networking is a feature that allows multiple Docker hosts/nodes to communicate with each other across different physical or virtual networks. It enables containers running on different hosts to be part of the same virtual network even if they reside on separate physical networks.
      <br/><br/>
      Overlay networking is a key component in Docker Swarm mode and is used to create a distributed network fabric that spans the Swarm cluster.
    </details>
1. ⭐⭐ How do you manage network connectivity between Docker containers and the host machine?
    <details> <summary>answer </summary>
      Docker manages network connectivity between containers and the host machine using virtual network interfaces. By default, Docker sets up a bridge network interface on the host that connects to containers using internal IP addresses. Containers can communicate with each other through the bridge network and with the outside world through the host machine's network interface.
      <br/><br/>
      Docker also provides options to create custom networks and specify network configurations, such as IP ranges, DNS settings, and port mappings, to control network connectivity between containers and the host.
    </details>
1. ⭐⭐ What is the purpose of the "docker network inspect" command?
    <details> <summary>answer </summary>
      The docker network inspect command is used to retrieve detailed information about a Docker network. It provides information such as the network's name, ID, driver, subnet configuration, connected containers, and other properties.

      This command is useful for troubleshooting network connectivity issues, verifying network configurations, and obtaining information about network interfaces and IP addresses associated with containers.
    </details>

## Docker volume
___________________________________________________________

1. ⭐ What are Docker volumes and how do they work?
    <details> <summary>answer </summary>
      Docker volumes are a way to persist and share data between containers and the host machine. They are directories or file systems that exist outside the container's writable layer. They can be created and managed using the docker volume command or by specifying them in a Docker Compose file.

      Docker volumes provide a reliable and efficient method for storing data that needs to persist beyond the lifetime of a container.
    </details>
1. ⭐⭐ How do you manage data persistence in Docker containers?
    <details> <summary>answer </summary>
      Docker provides several options for managing data persistence in containers. One approach is to use Docker volumes, which allow you to create and attach volumes to containers. This ensures that the data persists even if the container is removed or replaced.

      Another option is to mount host directories or files into containers using bind mounts, which provide a direct link between the container and the host file system. Additionally, you can leverage external storage systems or cloud-based solutions for storing data outside of the container environment.
    </details>
1. ⭐How do you update a Docker container without losing data?
    <details> <summary>answer </summary>
      The steps to update a Docker container without losing data are:

      - Create a backup of any important data stored within the container.
      - Stop the container gracefully using the docker stop command.
      - Pull the latest version of the container image using docker pull.
      - Start a new container using the updated image, making sure to map any necessary volumes or bind mounts.
      - Verify that the new container is functioning correctly and that the data is still intact.
    </details>

## Docker instructions:
___________________________________________________________

1. ⭐⭐ What is the difference between COPY and ADD instructions in a Dockerfile?
    <details> <summary>answer </summary>
      In a Dockerfile, the COPY instruction copies files or directories from the host machine to the container's filesystem. It is used for straightforward file copying.

      On the other hand, the ADD instruction has additional capabilities. It can copy local files, extract compressed archives (tar, gzip, etc.) into the container, and even download files from URLs and automatically unpack them. Due to its added complexity and potential security risks, it is generally recommended to use COPY unless the extra functionality of ADD is explicitly required.
    </details>
1. ⭐ What are Docker labels and how do you use them?
    <details> <summary>answer </summary>
      Docker labels are key-value metadata pairs that can be applied to Docker objects such as containers, images, and volumes. They provide a way to add custom metadata to these objects for organization, identification, and categorization purposes.

      Labels can be set using the --label flag when creating or modifying Docker objects. They can be accessed and filtered using various Docker commands like docker ps or docker inspect.
    </details>
1. ⭐⭐ How do you secure Docker containers?
    <details> <summary>answer </summary>
      Securing Docker containers involves implementing a multi-layered approach. Some recommended practices include:

      - Using trusted base images from reputable sources.
      - Regularly updating Docker and the underlying host system with security patches.
      - Employing secure configurations such as limiting container privileges and resources.
      - Scanning container images for vulnerabilities using security tools.
      - Implementing network segmentation and access controls.
      - Enforcing strong authentication and access controls for Docker daemon.
      - Monitoring container activity and logging for security analysis.
      - Applying the least privilege principle and implementing container-specific security measures like AppArmor or seccomp profiles.
    </details>

## Docker swarm
___________________________________________________________
1. ⭐⭐ How do you create a Docker Swarm cluster?
    <details> <summary>answer </summary>
      These are the steps to create a Docker Swarm cluster:

      - Initialize a Swarm on the manager node using the command docker swarm init.
      - Join worker nodes to the Swarm by running the command provided by the previous step on each worker node.
      - Verify the cluster status by executing docker node ls on the manager node. It should show the manager and worker nodes as part of the Swarm.
      - You now have a functioning Docker Swarm cluster and you can deploy and manage services using Swarm features.
    </details>
1. ⭐⭐ How do you manage secrets in Docker Swarm?
    <details> <summary>answer </summary>
      To manage secrets in Docker Swarm, you can follow these steps:

      - Create a secret using the docker secret create command, specifying the secret's name and the file or input source containing the secret data.
      - Deploy a service or update an existing service with access to the secret using the --secret flag in the docker service create or docker service update commands.
      - Within the service, the secret is made available as a file in the specified location (e.g., /run/secrets/<secret_name>). The service can read and use the secret from that file.
    </details>
1. ⭐⭐ How does Docker handle service discovery in Swarm mode?
    <details> <summary>answer </summary>
      In Docker Swarm mode, Docker provides an in-built service discovery mechanism. When you deploy a service to the Swarm cluster, it is automatically assigned a DNS name that can be used to reach the service internally.

      Each service is accessible via its service name within the Swarm. Docker's built-in DNS server automatically resolves the service name to the appropriate container IP addresses. This enables seamless communication between services within the cluster.
    </details>
1. ⭐⭐ How do you perform rolling updates in Docker Swarm?
    <details> <summary>answer </summary>
      Rolling updates in Docker Swarm can be performed by following these steps:

      - Update the service you're interested in by replacing its current Docker image with a newer version.
      - Use the docker service update command with the --update-parallelism and --update-delay flags to control the number of containers updated simultaneously and the delay between updates.
      - Docker Swarm will gradually update the containers in a rolling fashion, ensuring that the service remains available during the update process.
      - Monitor the update progress using docker service ps < service-name > and check for any issues or failures.
      - Once the update is complete, verify that the new version of the service is running as expected.
    </details>
1. ⭐⭐⭐ What are the differences between Docker Swarm and HashiCorp Nomad?
    <details> <summary>answer </summary>
      Docker Swarm and HashiCorp Nomad are container orchestration platforms, but they have some differences:

      - Swarm is Docker's built-in orchestration solution, tightly integrated with Docker. Nomad, on the other hand, is a general-purpose workload orchestrator that supports various types of workloads including containers.
      - Swarm is primarily focused on managing Docker containers, whereas Nomad supports multiple container runtimes including Docker. It also supports other workload types such as VMs and standalone executables.
      - Swarm provides a simpler and more straightforward setup and configuration compared to Nomad, which offers more flexibility and advanced features like job scheduling and multi-datacenter support.
      - Nomad has a more decentralized architecture with separate agents running on each node. Swarm follows a more centralized model with manager and worker nodes.
      - Swarm has tighter integration with other Docker tools and ecosystem components. Nomad offers a broader set of integrations and can be used with various infrastructure providers.
    </details>
1. ⭐⭐⭐ How do you achieve high availability with Docker Swarm?
    <details> <summary>answer </summary>
      High availability in Docker Swarm can be achieved by running multiple manager nodes in a swarm cluster. Manager nodes are responsible for managing the swarm state and coordinating tasks. By having multiple manager nodes, the swarm cluster can continue to operate even if one or more manager nodes become unavailable.

      Additionally, by using Docker's built-in swarm mode features like service replication, automatic container rescheduling, and distributed application placement, the swarm can distribute containers across multiple worker nodes. This ensures high availability and fault tolerance.
    </details>
1. ⭐⭐⭐ What are the differences between Docker Swarm and Kubernetes?
    <details> <summary>answer </summary>
      Docker Swarm and Kubernetes are both container orchestration platforms, but they have some key differences.

      Docker Swarm is Docker's native orchestration tool and is simpler to set up and use compared to Kubernetes. Swarm has a smaller learning curve and is ideal for smaller-scale deployments.

      On the other hand, Kubernetes is a more complex and feature-rich platform with a larger ecosystem. It provides advanced features like automatic scaling, rolling updates, service discovery, and load balancing. It is better suited for larger-scale and more complex deployments.
    </details>

1. ⭐⭐⭐ How do you implement blue-green deployments with Docker?
    <details> <summary>answer </summary>
      Blue-green deployments can be implemented with Docker by following these steps:

      - Set up two identical environments, each with its own Docker Swarm cluster.
      - Deploy the new version of your application to the "blue" environment.
      - Verify and test the new version in the "blue" environment.
      - Once the new version is deemed stable, switch the load balancer or routing rules to route traffic to the "blue" environment.
      - Monitor the application in the "blue" environment to ensure it is functioning correctly.
      - If any issues arise, you can quickly switch back to the "green" environment by reverting the load balancer or routing rules.
      - Once you are confident in the stability of the "blue" environment, the "green" environment can be updated with the new version for future deployments.
    </details>

1. ⭐⭐⭐ What is the purpose of the Docker Config feature in Swarm mode?
    <details> <summary>answer </summary>
      The Docker Config feature in Swarm mode allows you to manage and distribute configuration files to services running in the Swarm cluster. It decouples the configuration from the container image, making it easier to update configurations without rebuilding or redeploying containers.

      Configs can be created from a file or directly from an external data source. The Config feature ensures that services in the swarm cluster use the correct and up-to-date configurations. This simplifies the management of application settings and reduces the need for manual intervention.
    </details>

1. ⭐⭐⭐ How do you manage secrets in Docker using external secret stores?
    <details> <summary>answer </summary>
      Docker provides support for managing secrets securely using external secret stores. To manage secrets in Docker, you can:

      - Create or obtain the required secret values
      - Store the secrets in an external secret store like HashiCorp Vault or Azure Key Vault
      - Configure Docker to use the external secret store as the backend for secret management
      - Create secrets in Docker Swarm or Kubernetes using the Docker CLI or API, referencing the secret values stored in the external secret store
      - The secrets are securely retrieved at runtime and made available to the containers in a controlled manner, ensuring sensitive information remains protected.
    </details>

1. ⭐⭐⭐ How do you achieve load balancing with Docker Swarm?
    <details> <summary>answer </summary>
      Load balancing in Docker Swarm can be achieved by using the built-in load balancing feature. When a service is deployed in a Docker Swarm cluster, multiple containers are created to run the service.

      The Swarm's load balancer automatically distributes incoming requests across the available containers running the service, ensuring that the load is evenly distributed. This provides high availability and scalability for the application.

      Additionally, you can configure advanced load balancing options such as session stickiness and routing modes to customize the behavior of the load balancer according to your application's requirements.
    </details>








Sources: 

[github: docker-Interview-Questions-and-Answers](https://github.com/mrbardia72/docker-Interview-Questions-and-Answers/blob/main/docker-compose.md)

[interview-questions](https://www.turing.com/interview-questions/docker)