# Networking

## Overlay network
Docker overlay network is based on vxlan.
Since the overlay network in docker is guest another driver type we create it like any other network using `docker network create` for example: `docker network create --driver overlay --subnet 10.0.9.0/24 my-multi-host-network`.
When we create an overlay network a namespace is created for that network on every host that is connected to the cluster.
In each of these namespaces a vxlan interface is created. The vxlan interfaces are configured to match each other, that is they create a virtual lan between all the hosts in the cluster. We will refer to this network namespace as `vxlan-ns1`. Antoher imporant interface that is created inside `vxlan-ns1` is a bridge interface. This bridge simply forwards packets between all the interfaces that are connected to it. Since our network is currently empty only our vxlan interface is connected to it.

To add a container to the network that we just created we simply use the `--network` option. For example `docker run -itd --name=web --network=my-multi-host-network`. When we run this interface a `veth` device is added to `vxlan-ns1` and connected to the bridge.


### Resources

1. [Deep dive in docker overlay networks - DockerCon 2017](https://www.youtube.com/watch?v=b3XDl0YsVsg)
2. [Getting started with overlays - Docker official docs.](https://docs.docker.com/engine/userguide/networking/get-started-overlay/)
