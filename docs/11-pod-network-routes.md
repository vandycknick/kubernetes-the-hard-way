# Provisioning Pod Network Routes

Pods scheduled to a node receive an IP address from the node's Pod CIDR range. At this point pods can not communicate with other pods running on different nodes due to missing network [routes](https://cloud.google.com/compute/docs/vpc/routes).

In this lab you will create a route for each worker node that maps the node's Pod CIDR range to the node's internal IP address.

> There are [other ways](https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-achieve-this) to implement the Kubernetes networking model.

## The Routing Table

In this section you will gather the information required to create routes in the `kubernetes-the-hard-way` VPC network.

Print the internal IP address and Pod CIDR range for each worker instance:

```
for i in 0 1 2; do
  host_ip=$(vagrant ssh worker-${i} -c "ip -f inet addr show enp0s8 | awk '/inet / {print \$2}' | cut -d/ -f1" | tr -d '\r' | tr -d '\n')
  echo "$host_ip 10.200.1$i.0/24"
done
```

> output

```
10.240.0.20 10.200.10.0/24
10.240.0.21 10.200.11.0/24
10.240.0.22 10.200.12.0/24
```

## Routes

Create network routes for each worker instance:

```
for i in 0 1 2; do
  vagrant ssh worker-${i} --command "sudo apt-get update; sudo apt-get install -y net-tools;"
  vagrant ssh worker-${i} --command "sudo route add -net 10.200.10.0/24 gw 10.240.0.20"
  vagrant ssh worker-${i} --command "sudo route add -net 10.200.11.0/24 gw 10.240.0.21"
  vagrant ssh worker-${i} --command "sudo route add -net 10.200.12.0/24 gw 10.240.0.22"
done
```

List the routes in the `kubernetes-the-hard-way` VPC network:

```
for i in 0 1 2; do
  echo "Routes on worker-${i}"
  vagrant ssh worker-${i} --command "route"
  echo
done
```

> output

```
Routes on worker-0
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 enp0s3
_gateway        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s3
10.200.10.0     worker-0        255.255.255.0   UG    0      0        0 enp0s8
10.200.11.0     10.240.0.21     255.255.255.0   UG    0      0        0 enp0s8
10.200.12.0     10.240.0.22     255.255.255.0   UG    0      0        0 enp0s8
10.240.0.0      0.0.0.0         255.255.255.0   U     0      0        0 enp0s8

Routes on worker-1
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 enp0s3
_gateway        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s3
10.200.10.0     10.240.0.20     255.255.255.0   UG    0      0        0 enp0s8
10.200.11.0     worker-1        255.255.255.0   UG    0      0        0 enp0s8
10.200.12.0     10.240.0.22     255.255.255.0   UG    0      0        0 enp0s8
10.240.0.0      0.0.0.0         255.255.255.0   U     0      0        0 enp0s8

Routes on worker-2
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
default         _gateway        0.0.0.0         UG    100    0        0 enp0s3
10.0.2.0        0.0.0.0         255.255.255.0   U     0      0        0 enp0s3
_gateway        0.0.0.0         255.255.255.255 UH    100    0        0 enp0s3
10.200.10.0     10.240.0.20     255.255.255.0   UG    0      0        0 enp0s8
10.200.11.0     10.240.0.21     255.255.255.0   UG    0      0        0 enp0s8
10.200.12.0     worker-2        255.255.255.0   UG    0      0        0 enp0s8
10.240.0.0      0.0.0.0         255.255.255.0   U     0      0        0 enp0s8

```

Next: [Deploying the DNS Cluster Add-on](12-dns-addon.md)
