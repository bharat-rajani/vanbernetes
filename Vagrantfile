IMAGE_NAME = "generic/ubuntu2004"
K8S_NAME = "br-k8s-01"
MASTERS_NUM = 1
MASTERS_CPU = 2 
MASTERS_MEM = 2048

NODES_NUM = 2
NODES_CPU = 2
NODES_MEM = 2048

IP_BASE = "192.168.101."

VAGRANT_DISABLE_VBOXSYMLINKCREATE=1

Vagrant.configure("2") do |config|
    config.ssh.insert_key = false


    # config.trigger.before :destroy do |trigger|
    #     trigger.info = "Removing join command file"
    #     trigger.run = {inline: "bash -c 'cd roles; for filename in *-join-command; do echo $filename; mv $filename ${filename%.*}_$(date +%D_%T); done;'"}
    # end

    # config.trigger.after :resume do |trigger|
    #     trigger.info = "Removing join command file"
    #     trigger.run = {inline: "bash -c 'cd roles; for filename in *-join-command; do echo $filename; mv $filename ${filename%.*}_old; done;'"}
    # end
    
    (1..MASTERS_NUM).each do |i|      
        config.vm.define "k8s-master-#{i}" do |master|
            master.vm.box = IMAGE_NAME
            master.vm.network "private_network", ip: "#{IP_BASE}#{i + 10}"
            master.vm.hostname = "k8s-master-#{i}"
            master.vm.provider "libvirt" do |v|
                v.memory = MASTERS_MEM
                v.cpus = MASTERS_CPU
                v.qemu_use_session = false
            end            
            master.vm.provision "ansible" do |ansible|
                ansible.playbook = "roles/k8s.yml"
                #Redefine defaults
                ansible.extra_vars = {
                    k8s_cluster_name:       K8S_NAME,                    
                    k8s_master_admin_user:  "vagrant",
                    k8s_master_admin_group: "vagrant",
                    k8s_master_apiserver_advertise_address: "#{IP_BASE}#{i + 10}",
                    k8s_master_node_name: "k8s-master-#{i}",
                    k8s_node_public_ip: "#{IP_BASE}#{i + 10}"
                }                
            end
        end
    end

    (1..NODES_NUM).each do |j|
        config.vm.define "k8s-worker-#{j}" do |node|
            node.vm.box = IMAGE_NAME
            node.vm.network "private_network", ip: "#{IP_BASE}#{j + 10 + MASTERS_NUM}"
            node.vm.hostname = "k8s-worker-#{j}"
            node.vm.provider "libvirt" do |v|
                v.memory = NODES_MEM
                v.cpus = NODES_CPU
                v.qemu_use_session = false
                #v.customize ["modifyvm", :id, "--cpuexecutioncap", "20"]
            end             
            node.vm.provision "ansible" do |ansible|
                ansible.playbook = "roles/k8s.yml"
                # ansible.ask_become_pass = true                   
                #Redefine defaults
                ansible.extra_vars = {
                    k8s_cluster_name:     K8S_NAME,
                    k8s_node_admin_user:  "vagrant",
                    k8s_node_admin_group: "vagrant",
                    k8s_node_public_ip: "#{IP_BASE}#{j + 10 + MASTERS_NUM}"
                }
            end
        end
    end
end
