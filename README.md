#### orintcoreos
#####coreos subsystems
######etcd
basic commands
```
etcdctl set /mes ke      //k v map
etcdctl get /mes
etcdctl mkdir /folder
etcdctl set /folder/container1 localhost:1111
etcdctl ls /folder
etcdctl set /mes "expire soon" --ttl 10
etcdctl watch /folder --recursive
```
######fleet
stages:  
submit load start stop unload destroy   
commands:
```
fleetctl list-machines
fleetctl start redis.service
fleetctl journal redis.service
fleetctl --tunnel=10.1.1.1 list-machines
```

#####launching a deve coreos cluster
######systemd
login first
```
ssh core@ip
```
Then
```
sudo su
vim /etc/systemd/system/helloworld.service
```
edit
```
[Unit]
Description=Hello World Service
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill hello-world
ExecStartPre=-/usr/bin/docker rm hello-world
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name hello-world busybox /bin/sh -c "while true; do echo Hello World; sleep 1; done"
ExecStop=-/usr/bin/docker rm -f hello-world


[Install]
WantedBy=multi-user.target
```
start:
```
systemctl start helloworld.service
```
test
```
journalctl -f -u helloworld.service
```
create another service
```
vim /etc/systemd/system/helloworld2@.service
```
edit (%n=servicename %p=before@  %i=after@ %H=hostmachine)
```
[Unit]
Description=%n Service
After=docker.service
Requires=docker.service

[Service]
TimeoutStartSec=0
ExecStartPre=-/usr/bin/docker kill %p-%i
ExecStartPre=-/usr/bin/docker rm %p-%i
ExecStartPre=/usr/bin/docker pull busybox
ExecStart=/usr/bin/docker run --name %p-%i busybox /bin/sh -c "while true; do echo Hello from %p-%i running on %H; sleep 1; done"
ExecStop=-/usr/bin/docker rm -f %p-%i

[Install]
WantedBy=multi-user.target
```
use
```
systemctl start helloworld2@1.service
```
you can see output:
```
Hello from helloworld2-1 running on core-01
...
...
```

start instances:
```
systemctl start helloworld2@{1..4}.service
```
check
```
docker ps
```
stop instances:
```
systemctl stop helloworld2@{1..4}.service
```

######start units with fleet
```
fleetctl list-unit-files
fleetctl list-units
```
######etcd and etcdctl
```
etcdctl ls /
etcdctl ls / recursive
```
make a key
```
edctctl mkdir /my
etcdctl mk /my/key value
etcdctl get /my/key
etcdctl update /my/key newvalue
etcdctl set /my/key newvalue
etcdctl rm /my/key
######troubleshooting
```
systemctl status -l fleet
journalctl -b -u etcd
```
