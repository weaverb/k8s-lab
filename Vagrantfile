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
        cp.vm.network "private_network", ip: "172.16.50.10"
        cp.vm.hostname = "c1-cp1"
        cp.vm.provision "shell", inline: <<-SHELL
            sed -ie '/c1-cp1/d' /etc/hosts
            echo "172.16.50.10  c1-cp1" >> /etc/hosts
            echo "172.16.50.11  c1-node1" >> /etc/hosts
            echo "172.16.50.12  c1-node2" >> /etc/hosts
            echo "172.16.50.13  c1-node3" >> /etc/hosts
        SHELL
        cp.vm.provision "ansible" do |ansible|
            ansible.playbook = "k8s-setup/cp-playbook.yml"
            ansible.extra_vars = {
                node_ip: "172.16.50.10",
            }
        end
    end

    (1..NODES).each do |i|
        config.vm.define "c1-node#{i}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "172.16.50.#{i + 10}"
            node.vm.hostname = "c1-node#{i}"
            node.vm.provision "shell", inline: <<-SHELL
                sed -ie "/$(hostname)/d" /etc/hosts
                echo "172.16.50.10  c1-cp1" >> /etc/hosts
                echo "172.16.50.11  c1-node1" >> /etc/hosts
                echo "172.16.50.12  c1-node2" >> /etc/hosts
                echo "172.16.50.13  c1-node3" >> /etc/hosts
            SHELL
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "k8s-setup/node-playbook.yml"
                ansible.extra_vars = {
                    node_ip: "172.16.50.#{i + 10}",
                }
            end
        end
    end
end