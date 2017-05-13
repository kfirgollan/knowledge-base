# Networking

## Namespacing

Docker containers have dedicated network namespaces created by docker.
This namespaces are not visible using the `ip netns ls` by default. This is because the list of currently active namespaces that is maintained in `/var/run/netns` is not updated by docker.
To allow visualization of these namespaces the following commands can be used:

```
pid=`docker inspect -f '{{.State.Pid}}' $container_id`
ln -s /proc/$pid/ns/net /var/run/netns/$container_idhttp://stackoverflow.com/questions/31265993/docker-networking-namespace-not-visible-in-ip-netns-list
```

Taken from: http://stackoverflow.com/questions/31265993/docker-networking-namespace-not-visible-in-ip-netns-list

## Overlay network

Docker overlay network is based on vxlan.
Since the overlay network in docker is just another driver type we create it like any other network using `docker network create` for example: `docker network create --driver overlay --subnet 10.0.9.0/24 my-multi-host-network`.
When we create an overlay network a namespace is created for that network on every host that is connected to the cluster.

In each of these namespaces a vxlan interface is created. The vxlan interfaces are configured to match each other, that is they create a virtual lan between all the hosts in the cluster. We will refer to this network namespace as `vxlan-ns1`. Antoher imporant interface that is created inside `vxlan-ns1` is a bridge interface. This bridge simply forwards packets between all the interfaces that are connected to it. Since our network is currently empty only our vxlan interface is connected to it.

To add a container to the network that we just created we simply use the `--network` option. For example `docker run -itd --name=web --network=my-multi-host-network`. When we run this interface a `veth` device is added to `vxlan-ns1` and connected to the bridge.


### Resources

1. [Deep dive in docker overlay networks - DockerCon 2017](https://www.youtube.com/watch?v=b3XDl0YsVsg)
2. [Getting started with overlays - Docker official docs.](https://docs.docker.com/engine/userguide/networking/get-started-overlay/)
