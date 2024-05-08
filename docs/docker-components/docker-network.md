To handle Docker - we need to use driver:

Detauls drivers: `none`, `bridge`, `host`, `overlay`, `macvlan`

Get awailable networks in docker: `docker network ls`

To get details for network: `docker network inspect none`
- `none`: totally isolate docker container
```sh
    # try youtself
    docker container run --rm -d --it --name isolated_container --network alphine

    docker container exec isolated_container ping www.google.com
    docker container exec isolated_container ping 8.8.8.8

    #result - network is unreachable

```
- `bridge`: allow to isolate a group of containers from other containers in host machine. That group of containers would have internet access
```sh
    docker network test_bridge1
    docker network test_bridge2
    docker container run --rm -d --it --name bridge_container1 --network test_bridge1
    docker container run --rm -d --it --name bridge_container2 --network test_bridge2
```
- `host` - with using this network it doesn't isolate docker network and doesn't provide separate IP address
    Halpfull to use this if there are a lot of ports need to be exposed from container
    Ca be used only on `Linux` like machine

- `overlay` - not so popular. Allow to connect between docker deamons. For example in docker-swarm.
This network sits on top of (overlays) the host-specific networks, allowing containers connected to it to communicate securely when encryption is enabled. Docker transparently handles routing of each packet to and from the correct Docker daemon host and the correct destination container.

The term “overlay” has come to be used extensively (instead of VPN) only after technologies different than PPTP or L2TP have been developed to run virtual networks in Cloud environments. For those environments, protocols like VXLAN or GENEVE have been developed to address specific needs.

```sh 
#example to use
docker network create \
  --driver overlay \
  --ingress \
  --subnet=10.11.0.0/16 \
  --gateway=10.11.0.2 \
  --opt com.docker.network.driver.mtu=1200 \
  my-ingress
```
```sh
 docker network create -d overlay --attachable my-attachable-overlay
```

- `Macvlan` - we can assign mac address for container - in network is used like a separate device.
Allows you to work with applications that require a direct connection to the physical network.

Articles:

- [EN] [What is Docker networking?](https://www.oreilly.com/content/what-is-docker-networking/)
- [EN] [Docker networks](https://docs.docker.com/network/drivers/overlay/)
https://medium.com/be-tech-with-santander/using-docker-overlay-networks-configuration-guide-526b469befa4
- [RU] [Docker and network?](https://habr.com/ru/companies/otus/articles/730798/)
- [RU] [Overlay connection in Docker swarm](https://habr.com/ru/articles/334004/)

Videos: 
 - [EN] [The Overlay Network Driver | Networking in Docker](https://www.youtube.com/watch?v=gJVcKVdYhpI)