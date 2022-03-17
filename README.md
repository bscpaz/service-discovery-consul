<h1 align="center">Service Discovery with Consul</h1>
<p align="center">This is a POC (proof of concept) to understand better the behavior of Consul technology.</p>


### Technologies:
* Consult (https://www.consul.io)

### Creating a Consul server
```console
docker-compose up -d
```
##### Stopping a Consul server
```console
docker-compose stop
```
##### Removing a Consul server
```console
docker-compose down
```
##### Getting into Consul server
```console
docker exec -it consulserver01 sh
```
###### Creating config folders
```console
mkdir /var/lib/consul
```
```console
mkdir /etc/consul.d
```
###### Installing 'dig' command for check DNS informations
```console
apk -U add bind-tools
```
###### Initializing Consul server
* Checking the current IP
```console
ifconfig
```
* Starting a consul server (without config files)
```console
consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=<IP> -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```
* Creating a cluster
```console
consul join <IP of some other consul server>
```

### Creating a Consul client
```console
docker exec -it consulclient01 sh
```
###### Creating config folders
```console
mkdir /var/lib/consul
```
```console
mkdir /etc/consul.d
```
###### Installing 'dig' command for check DNS informations
```console
apk -U add bind-tools
```
###### Initializing Consul client
* Checking the current IP
```console
ifconfig
```
* Starting a consul server (without config files)
```console
consul agent -bind=<IP> -data-dir=/var/lib/consul -config-dir=/etc/consul.d -retry-join=<IP consul server #1> -retry-join=<IP consul server #2>
```
* Registring to a cluster server
```console
consul join <IP of some other consul server>
```

### Checking Informations
###### Checking Consul's members
```console
consul members
```
###### Checking nodes catalog
```console
consul catalog nodes -detailed
```
```console
curl localhost:8500/v1/catalog/nodes
```
###### Checking services catalog
```console
consul catalog nodes -service nginx
```
```console
curl localhost:8500/v1/catalog/services
```
###### Showing all IPs members that are registred into Consul
```console
dig @localhost -p 8600
```
###### Show all IPs of a specific name of DNS
Run in client instance
```console
dig @localhost -p 8600 nginx.service.consul
```
The result will be something like this (two IPs for nginx service)
```console
;; ANSWER SECTION:
nginx.service.consul.   0       IN      A       172.21.0.5
nginx.service.consul.   0       IN      A       172.21.0.6
```
###### Reload configs
```console
consul reload
```
