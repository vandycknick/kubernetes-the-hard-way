# Provisioning Compute Resources

Kubernetes requires a set of machines to host the Kubernetes control plane and the worker nodes where containers are ultimately run. In this lab you will provision the compute resources required for running a secure and highly available Kubernetes cluster. All VMs are currently configured in the `Vagrantfile`, more on this later.

## Networking

The Kubernetes [networking model](https://kubernetes.io/docs/concepts/cluster-administration/networking/#kubernetes-model) assumes a flat network in which containers and nodes can communicate with each other. In cases where this is not desired [network policies](https://kubernetes.io/docs/concepts/services-networking/network-policies/) can limit how groups of containers are allowed to communicate with each other and external network endpoints.

> Setting up network policies is out of scope for this tutorial.

### The Network

This tutorial isn't using a cloud provider so we can't use a VPC to segregate the network. All networking is automatically configured in teh `Vagrantfile`. We will use the following network ranges:

- `10.240.0.0/24` This network can host up to 254 instances and will primary used for running workloads (controllers, workers)
- `10.200.0.0/16` This is a larger network range that isn't any configuration files yet but will be used in Kuberentes as an overlay network to provide IPs to pods running within the cluster.

### Kubernetes Controllers

In the Vagrant file you can find the following section:

```ruby
(0..controllers - 1).each do |n|
    config.vm.define "controller-#{n}" do |controller|
        controller.vm.box = "ubuntu/focal64"
        controller.vm.box_version = "20231207.0.0"
        controller.vm.hostname = "controller-#{n}"

        controller.vm.network :private_network, ip: "10.240.0.1#{n}", auto_config: true

        controller.vm.provider "virtualbox" do |v|
        v.memory = 1024
        v.cpus = 2
        end
    end
end
```

By default it will spin up 3 controller nodes and configure it and assign it an IP. It's possible tweak this and spin up less controller nodes depending on the available resources on your machine.

### Kubernetes Workers

In the Vagrant file you can find the following section:

```ruby
(0..workers - 1).each do |n|
    config.vm.define "worker-#{n}" do |worker|
        worker.vm.box = "ubuntu/focal64"
        worker.vm.box_version = "20231207.0.0"
        controller.vm.hostname = "worker-#{n}"

        worker.vm.network :private_network, ip: "10.240.0.2#{n}", auto_config: true

        worker.vm.provider "virtualbox" do |v|
            v.memory = 2048
            v.cpus = 2
        end
    end
end
```

By default it will spin up 3 worker nodes and configure it and assign it an IP. It's possible tweak this and spin up less controller nodes depending on the available resources on your machine.

### Verification

List running instances via the `vagrant` cli

```
vagrant status
```

> output

```
Current machine states:

controller-0              running (virtualbox)
controller-1              running (virtualbox)
controller-2              running (virtualbox)
worker-0                  running (virtualbox)
worker-1                  running (virtualbox)
worker-2                  running (virtualbox)

This environment represents multiple VMs. The VMs are all listed
above with their current state. For more information about a specific
VM, run `vagrant status NAME`.
```

## Configuring SSH Access

Vagrant automatically configures SSH keys to easily access worker or controller nodes. To access on of the nodes just find the name (controller or worker) and the correct index.

Test SSH access to the `controller-0` node:

```
vagrant ssh controller-0
```

> output

```
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.4.0-167-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Jan 10 13:13:52 UTC 2024

  System load:  0.0               Processes:               113
  Usage of /:   3.7% of 38.70GB   Users logged in:         0
  Memory usage: 20%               IPv4 address for enp0s3: 10.0.2.15
  Swap usage:   0%                IPv4 address for enp0s8: 10.240.0.10


Expanded Security Maintenance for Applications is not enabled.

0 updates can be applied immediately.

Enable ESM Apps to receive additional future security updates.
See https://ubuntu.com/esm or run: sudo pro status


The list of available updates is more than a week old.
To check for new updates run: sudo apt update
New release '22.04.3 LTS' available.
Run 'do-release-upgrade' to upgrade to it.


vagrant@controller-0:~$
```

Type `exit` at the prompt to exit the `controller-0` compute instance:

```
vagrant@controller-0:~$ exit
```

> output

```
logout
Connection to XX.XX.XX.XXX closed
```

Next: [Provisioning a CA and Generating TLS Certificates](04-certificate-authority.md)
