# Kubernetes lab using Vagrant

Creates a production-like cluster using Vagrant and VirtualBox. Quickly work with multi node clusters when minikube is not enough.

This cluster is based on kubernetes 1.20.1 and uses containerd as the cri and calico as the pod network.

## Pre-reqs

- Ansible: `brew install ansible`
- Vagrant: `brew tap hashicorp/tap && brew install vagrant`
- VirtualBox: https://www.virtualbox.org/wiki/Downloads

## Setup

The Vagrantfile is configured to bootstrap the control plane on `192.168.50.10` and the thre nodes on `192.168.50.11 ..12 ..13`, etc. If you need to change that modify the Vagrantfile and change the `k8s-setup/files/cluster/ClusterConfiguration.yml` file at line #12 to match the control plane ip.

Run the `vagrant up` command to bootstrap the cluster.

## Notes

If you want to connect to the cluster from the host (useful for tools like [Lens](https://k8slens.dev/) or a local kubectl), copy the kube-config from the control plane: `scp vagrant@192.168.50.10:/home/vagrant/.kube/config $HOME/.kube/config`. If you have multiple kube config connections that you dont want to overwrite, you can copy it to something like `$HOME/.kube/vagrant` and then set the `KUBECONFIG=:vagrant` env to merge it.
