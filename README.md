<h1 align="center">Service Discovery with Consul</h1>
<p align="center">This is a POC (proof of concept) to understand better the behavior of Consul technology.</p>


### Technologies:
* Consult (https://www.consul.io)

### How to get stated
#### Creating a Consul server
```console
docker-compose up -d
```
#### Getting into Consul server
```console
docker exec -it consul01 sh
```
##### Creating config folders
```console
mkdir /var/lib/consul
```
```console
mkdir /etc/consul.d
```
##### Installing 'dig' command for check DNS informations
```console
apk -U add bind-tools
```
##### Initializing Consul server
* Checking the current IP
```console
ifconfig
```
* Starting a consul server (without config files)
```console
consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=<IP> -data-dir=/var/lib/consul -config-dir=/etc/consul.d
```

#### Checking Informations
##### Checking Consul's members
```console
consul members
```
##### Checking Consul's catalog
```console
curl localhost:8500/v1/catalog/nodes
```
##### Showing all IPs members that are registred into Consul
```console
dig @localhost -p 8600
```
##### Show all IPs of a specific name of DNS
```console
dig @localhost -p 8600 consul01.node.consul
```
