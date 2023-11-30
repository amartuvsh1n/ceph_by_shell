source [link](https://docs.ceph.com/en/pacific/cephadm/host-management/#adding-hosts)

ceph ni full privledged usereer yavdag tul 'root' user hereglev
```bash
# set root user password
passwd root
# enable ssh by 'root' user
sed -i 's/PermitRootLogin/PermitRootLogin yes #/g'  /etc/ssh/sshd_config
sed -i 's/PasswordAuthentication/PasswordAuthentication yes #/g'  /etc/ssh/sshd_config
sudo systemctl restart sshd
```

```bash
ssh-copy-id -f -i /etc/ceph/ceph.pub root@node02
# root user password
```

Add host
```bash
ceph orch host add node02 192.168.12.203 
# Added host 'node02' with addr '192.168.12.203'
# if need label
ceph orch host add node02 192.168.12.203  --labels _admin
```

# verify
```bash
ceph orch host ls
```
