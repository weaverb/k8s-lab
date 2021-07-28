IMAGE_NAME = "ubuntu/bionic64"
NODES = 3

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = 2048
        v.cpus = 2
    end

    config.vm.define "c1-cp1" do |cp|
        cp.vm.box = IMAGE_NAME
        cp.vm.network "private_network", ip: "192.168.50.10"
        cp.vm.hostname = "c1-cp1"
        cp.vm.provision "ansible" do |ansible|
            ansible.playbook = "k8s-setup/cp-playbook.yml"
            ansible.extra_vars = {
                node_ip: "192.168.50.10",
            }
        end
    end

    (1..NODES).each do |i|
        config.vm.define "c1-node#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "192.168.50.#{i + 10}"
            node.vm.hostname = "c1-node#{i}"
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "k8s-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "192.168.50.#{i + 10}",
                }
            end
        end
    end
end