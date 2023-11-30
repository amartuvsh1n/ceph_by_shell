# Ceph intall on shell
This manual describes the installation of Ceph storage installing by cephadm.
Source [link](https://docs.ceph.com/en/latest/cephadm/install)

## step 1
Install packages and dependencies
```bash
apt update
apt install -y cephadm ceph-common
```

## step 2
Start Ceph in standalone mode
```bash
cephadm bootstrap --mon-ip 192.168.12.202 --initial-dashboard-user tbs --initial-dashboard-password P@ssw0rd --dashboard-password-noupdate --cluster-network 192.168.12.0/24 --single-host-defaults >> ceph_installation.log 2>&1 
```
Don't forgot, save your 'dashboard password'. 

The _--single-host-defaults_ flag sets the following configuration options:
```
global/osd_crush_chooseleaf_type = 0
global/osd_pool_default_size = 2
mgr/mgr_standby_modules = False
```

### Verify & show status
```bash
ceph -v
# output like below
# ceph version 17.2.6 (d7ff0d10654d2280e08f1ab989c7cdf3064446a5) quincy (stable)

ceph -s
# output like below
#    cluster:
#    id:     aee5a38e-8f4b-11ee-a720-cb9b6206f709
#    health: HEALTH_WARN
#            OSD count 0 < osd_pool_default_size 2
# 
#  services:
#    mon: 1 daemons, quorum node01 (age 4m)
#    mgr: node01.sgjcic(active, since 3m), standbys: node01.cikgsr
#    osd: 0 osds: 0 up, 0 in
# 
#  data:
#    pools:   0 pools, 0 pgs
#    objects: 0 objects, 0 B
#    usage:   0 B used, 0 B / 0 B avail
#    pgs:     
#
```

## add osd (disk)

```bash
# display all disks
ceph orch device ls

# Tell Ceph to consume any available and unused storage device (all avialable disks join to cluster)
ceph orch apply osd --all-available-devices
# or add manually
ceph orch daemon add osd node01:/dev/sdd

# if remote node disk's 
# 1st add node [guide](ADD_HOST.md)
ceph orch apply osd --all-available-devices
# or manually
ceph orch daemon add osd node02:/dev/sdd
ceph orch daemon add osd node02:/dev/sde

# display all osd
ceph osd tree
```

## Enable web dashboard
```bash
# check all service and states
ceph mgr module ls
# enable dashboard
ceph mgr module enable dashboard
```
##  OpenStack integration
...

# Additional optoins
```bash
# Enable memory autotuning:
ceph config set osd osd_memory_target_autotune true

# Dashboar [ssl](https://docs.ceph.com/en/latest/cephadm/install/#deployment-with-ca-signed-ssh-keys)
```
