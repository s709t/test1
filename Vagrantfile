image = "bento/ubuntu-18.04"
cluster = "k8s-01"
m_nodes = 1
mem = 1024
cpu = 2
n_nodes = 1
ip = "192.168.50."

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false

    config.vm.provider "virtualbox" do |v|
        v.memory = mem
        v.cpus = cpu
    end

    (1..m_nodes).each do |i|      
        config.vm.define "m-#{i}" do |master|
            master.vm.box = image
            master.vm.network "private_network", ip: "#{ip}#{i + 10}"
            master.vm.hostname = "m-#{i}"
            master.vm.provision "file", source: "scripts/listPods.py", destination: "/home/vagrant/tools/listPods.py"
            master.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "roles/k8s.yml"
                ansible.groups = {
                    "k8s-master" => ["m-[1:#{m_nodes}]"],
                    "k8s-node" => ["n-[1:#{n_nodes}]"]
                }
                ansible.extra_vars = {
                    k8s_cluster_name:       cluster,                    
                    k8s_master_admin_user:  "vagrant",
                    k8s_master_admin_group: "vagrant",
                    k8s_master_apiserver_advertise_address: "#{ip}#{i + 10}",
                    k8s_master_node_name: "m-#{i}",
                    k8s_node_public_ip: "#{ip}#{i + 10}"
                }                
            end
        end
    end

    (1..n_nodes).each do |j|
        config.vm.define "n-#{j}" do |node|
            node.vm.box = image
            node.vm.network "private_network", ip: "#{ip}#{j + 10 + m_nodes}"
            node.vm.hostname = "n-#{j}"
            node.vm.provision "ansible_local" do |ansible|
                ansible.playbook = "roles/k8s.yml"         
                ansible.groups = {
                    "k8s-master" => ["m-[1:#{m_nodes}]"],
                    "k8s-node" => ["n-[1:#{n_nodes}]"]
                }                    
                ansible.extra_vars = {
                    k8s_cluster_name:     cluster,
                    k8s_node_admin_user:  "vagrant",
                    k8s_node_admin_group: "vagrant",
                    k8s_node_public_ip: "#{ip}#{j + 10 + m_nodes}"
                }
            end
        end
    end
end    