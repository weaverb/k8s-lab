# Kubernetes lab using Vagrant

Creates a production-like cluster using Vagrant and VirtualBox. Quickly work with multi node clusters when minikube is not enough.

This cluster is based on kubernetes 1.20.1 and uses containerd as the cri and calico as the pod network.

## Pre-reqs

- Ansible: `brew install ansible`
- Vagrant: `brew tap hashicorp/tap && brew install vagrant`
- VirtualBox: https://www.virtualbox.org/wiki/Downloads

## Setup

The Vagrantfile is configured to bootstrap the control plane on `172.16.50.10` and the three nodes on `172.16.50.11 ..12 ..13`, etc. If you need to change that modify the Vagrantfile and change the `ClusterConfiguration.yml` file [HERE](https://github.com/weaverb/k8s-lab/blob/cd3ff6a14afc9b0521e82c163b67ad92a7f5845a/k8s-setup/files/configs/cluster/ClusterConfiguration.yaml#L12) to match the control plane ip.

Run the `vagrant up` command to bootstrap the cluster.

## Notes

If you want to connect to the cluster from the host (useful for tools like [Lens](https://k8slens.dev/) or a local kubectl), copy the kube-config from the control plane (havent figured out how to configure this through vagrant).

1. Goto the settings for the control plane VM in VirtualBox
2. Under the Network tab >> Adapter 1 (make sure it is NAT)
3. Expand "Advanced" and click the port forwarding button
4. add the SSH rule with
   - Protocol: TCP
   - Host IP: 127.0.0.1
   - Host Port: 2222
   - Guest IP: 172.16.50.10 (or whatever you set for c1-cp1)
   - Guest Port: 22
5. add the control plane rule with (this will allow you to use kubectl on the host)
   - Protocol: TCP
   - Host IP: 127.0.0.1
   - Host Port: 6443
   - Guest IP: 172.16.50.10 (or whatever you set for c1-cp1)
   - Guest Port: 6443

Now you can copy the kubeconfig from the vm to the host

`scp -P 2222 vagrant@127.0.0.1:/home/vagrant/.kube/config $HOME/.kube/config`.

If you have multiple kube config connections that you dont want to overwrite, you can copy it to something like `$HOME/.kube/vagrant` and then set the `KUBECONFIG=$HOME/.kube/vagrant` env to merge it.

Helpful tools:

- `krew`: kubectl plugin package manager. [Get it here](https://github.com/kubernetes-sigs/krew/)
- `kubectx` && `kubens`: utilities to quickly switch between contexts and namespaces. [Get it here](https://github.com/ahmetb/kubectx)
