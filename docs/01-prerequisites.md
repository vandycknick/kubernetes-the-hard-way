# Prerequisites

## Vagrant

This version of the [Kubernetes-The-Hard-Way]() relies Vagrant to spin up VMs on your local machine through Virtualbox.

### Install the Vagrant

To get started with Vagrant, download the appropriate installer or package for your platform from the [Vagrant](https://developer.hashicorp.com/vagrant/downloads) downloads page. Install the package with the standard procedures for your operating system.

Verify the Vagrant version is 2.3.7 or higher:

```
vagrant --version
```

### Install Virtualbox

Vagrant is the command line utility for managing the lifecycle of virtual machines, but doesn't come with a Hypervisor. Because of this you still need to install `Virtualbox` for your operating system from [virtualbox.org](https://www.virtualbox.org/).

## Running Commands in Parallel with tmux

[tmux](https://github.com/tmux/tmux/wiki) can be used to run commands on multiple compute instances at the same time. Labs in this tutorial may require running the same commands across multiple compute instances, in those cases consider using tmux and splitting a window into multiple panes with synchronize-panes enabled to speed up the provisioning process.

> The use of tmux is optional and not required to complete this tutorial.

![tmux screenshot](images/tmux-screenshot.png)

> Enable synchronize-panes by pressing `ctrl+b` followed by `shift+:`. Next type `set synchronize-panes on` at the prompt. To disable synchronization: `set synchronize-panes off`.

Next: [Installing the Client Tools](02-client-tools.md)
