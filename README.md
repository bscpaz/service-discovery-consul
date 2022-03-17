<h1 align="center">Service Discovery with Consul</h1>
<p align="center">This is a POC (proof of concept) to understand better the behavior of Consul technology.</p>


### Technologies:
* Consult (https://www.consul.io)
* nginx (in clients)

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
* 'bootstrap-expect' means the number of Consul server will be running.
```console
consul agent -server -bootstrap-expect=3 -node=consulserver01 -bind=<IP> -data-dir=/var/lib/consul -config-dir=/etc/consul.d -retry-join=<IP consul server #1> -retry-join=<IP consul server #2>
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
* If you just mention 'agent', Consul knows that it is a client instance.
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
### Installing and starting nginx on client
##### Installing softwares
```console
apk add nginx
```
```console
apk add vim
```
##### Initial configurations
```console
mkdir /run/nginx
```
```console
mkdir /usr/share/nginx/html -p
```
##### Changing nginx 'default.conf' file to not return 404
```console
vim /etc/nginx/conf.d/default.conf
```
###### Remove the following:
```console
# Everything is a 404
location / {
  return 404;
}
```
###### Add the following:
```console
root /usr/share/nginx/html;
```
###### Create some html page to not return 404:
```console
vim /usr/share/nginx/html/index.html
```
```html
<html>
  <body>
    Hello Bruno Costa Paz!
  </body>    
</html>   
```
##### Starting nginx:
```console
nginx
```
###### If alread started, you can reload it:
```console
nginx -s reload
```
##### Checking the execution of nginx
```console
curl localhost
```
