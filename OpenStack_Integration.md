# At the outset of this guide, it is imperative to acknowledge the integral role played by Ceph RBD storage in the orchestration of data storage for key OpenStack services such as Nova, Cinder, and Glance. These services collectively form the backbone of a cohesive system that efficiently manages and stores data within the OpenStack ecosystem.

## Create pool
Ceph storage has storage groups called pool. 
```bash
ceph osd pool create volumes  128 
ceph osd pool create images  64
ceph osd pool create backups  128
ceph osd pool create vms  32
```
set pool  type to rbd. Radius Block Device(rbd) is block storage type of Ceph. 
```bash
rbd pool init volumes
rbd pool init images
rbd pool init backups
rbd pool init vms
```

# Create Ceph users for OpenStack services, Nova will use cinder user.
```bash
ceph auth get-or-create client.glance mon 'profile rbd' osd 'profile rbd pool=images' mgr 'profile rbd pool=images'
ceph auth get-or-create client.cinder mon 'profile rbd' osd 'profile rbd pool=volumes, profile rbd pool=vms, profile rbd-read-only pool=images' mgr 'profile rbd pool=volumes, profile rbd pool=vms'
ceph auth get-or-create client.cinder-backup mon 'profile rbd' osd 'profile rbd pool=backups' mgr 'profile rbd pool=backups'
```
Save user's keyrings in ceph config dir and OpenStack deployment host
```bash
ssh {deployment} sudo mkdir /etc/ceph
ceph auth get-or-create client.glance | ssh {deployment} sudo tee /etc/ceph/ceph.client.glance.keyring
ceph auth get-or-create client.cinder | ssh {deployment} sudo tee /etc/ceph/ceph.client.cinder.keyring
ceph auth get-or-create client.cinder-backup | ssh {deployment} sudo tee /etc/ceph/ceph.client.cinder-backup.keyring
```

[source](https://docs.openstack.org/kolla-ansible/latest/reference/storage/external-ceph-guide.html)

```bash
ssh {deployment}
# using venv is optional 
source venv/bin/activate

# edit globals.yml


# copy ceph config files to kolla-ansible config dir
mkdir -p /etc/kolla/config/nova
mkdir -p /etc/kolla/config/glance
mkdir -p /etc/kolla/config/cinder/cinder-volume
mkdir -p /etc/kolla/config/cinder/cinder-backup

```
