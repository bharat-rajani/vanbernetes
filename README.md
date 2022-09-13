# Vanbernetes

Vanbernetes is kubernetes deployed locally using vagrant and ansible.

Vagrant spins up VMs, configures the network and ansible provisions the kubernetes cluster using kubeadm.

Inside the project directory run:


``` sh
vagrant up --provider=libvirt 
```