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
