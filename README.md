# Buid three_nodes_cluster

Create a three nodes openPBS HPC cluster on local windows desktop by using vagrant and ansible, pure *100%* code (IoC)

## Prerequisite:
Reference this Repository to setup [local vagrant VM development environment](https://github.com/yjun-001/vagrant_vm_windows10)

## Cluster Inventory file:

https://github.com/yjun-001/vagrant_ansible_three_nodes_cluster/blob/9637f676be8b0d188f076783c5155ff5d4d6c07b/ansible/inventory/hosts#L1-L10

## Cluster Nodes build/provision process:
- vagrant create three virtual box VMs (one master node, two nodes)
  - using vagrant ubuntu 20.04 image 
- once VMs generated, ansible playbook kick-in for post-configuration
  - add cluster admin user (pbsadmin)
  - update /etc/hosts by hostname and cluster IPs on each nodes
  - enable SSH Authentication between each hosts, so each host can ssh other without password
- [Todo]: install openPBS package
 
