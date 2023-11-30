# Ceph intall on shell
Ceph storage installing by cephadm

source [link](https://docs.ceph.com/en/latest/cephadm/install/#cephadm-install-curl)

## step 1
Install packages
```bash
apt update
apt install -y cephadm ceph-common
```

## step 2
start stand alone
```bash
cephadm bootstrap --mon-ip 192.168.12.202 --initial-dashboard-user tbs --initial-dashboard-password P@ssw0rd --dashboard-password-noupdate --cluster-network 192.168.12.0/24 --single-host-defaults >> ceph_installation.log 2>&1 
```
copy dashbaord password 

The --single-host-defaults flag sets the following configuration options:
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

# Tell Ceph to consume any available and unused storage device
ceph orch apply osd --all-available-devices

# display all osd
ceph osd tree
```
