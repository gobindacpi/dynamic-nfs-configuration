# dynamic-nfs-configuration
~~~
Install NFS Server on Ubuntu 22.04
=======================================
apt update -y
apt install nfs-kernel-server -y

mkdir -p /mnt/NFS-BANK
chown -R nobody:nogroup /mnt/NFS-BANK/
chmod 777 /mnt/NFS-BANK/

vim /etc/exports
/mnt/NFS-BANK 192.168.56.0/24(rw,sync,no_subtree_check)   ;;save and exit

exportfs -a
systemctl restart nfs-kernel-server

Validity Check
=========================
root@master-node:~# showmount -a
All mount points on master-node:
root@master-node:~# showmount -e
Export list for master-node:
/mnt/NFS-BANK 192.168.56.0/24

For Client VM/Server
==========================
apt install nfs-common

mkdir -p /mnt/nfs_bank
mount 192.168.56.50:/mnt/NFS-BANK /mnt/nfs_bank

Validity check from client 
root@worker-node01:~# showmount -e 192.168.56.50
Export list for 192.168.56.50:
/mnt/NFS-BANK 192.168.56.0/24

root@worker-node02:~#  showmount -e 192.168.56.50
Export list for 192.168.56.50:
/mnt/NFS-BANK 192.168.56.0/24


Now install Storage Class on kubernertes by nfs and helm
===================================================================
subdir nfs provissioneer install url
https://github.com/kubernetes-sigs/nfs-subdir-external-provisioner
#snap install helm --classic
$ helm repo add nfs-subdir-external-provisioner https://kubernetes-sigs.github.io/nfs-subdir-external-provisioner/
$ helm install nfs-subdir-external-provisioner nfs-subdir-external-provisioner/nfs-subdir-external-provisioner \
    --set nfs.server=192.168.56.50 \
    --set nfs.path=/mnt/NFS-BANK


then check by bellow command
root@master-node:~/Kubernetes/Storage-Class# kubectl get sc
NAME         PROVISIONER                                     RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
nfs-client   cluster.local/nfs-subdir-external-provisioner   Delete          Immediate           true                   7s









~~~
