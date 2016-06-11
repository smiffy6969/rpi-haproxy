# ulsmith/rpi-haproxy:1.6.5

Raspberry pi image for standard off the shelf haproxy @ 1.6.5.


Built on rasberry pi zero, but should work for pi2 also, not sure about pi3 as it's 64bit.


# Usage


As stated, it's basic no frills haproxy, so you will need to configure it which you can do from the docs [http://www.haproxy.org/#docs] or by following the quick guide below.


Create a new docker folder to house your haproxy build script and config, two files, haproxy.cfg and Dockerfile


example haproxy.cfg
```bash
global
    maxconn 10000

defaults
    mode http
    timeout connect 5000ms
    timeout client 50000ms
    timeout server 50000ms

frontend http-in
    bind *:80

    # Define hosts
    acl docker-one hdr(host) -i localhost

    # figure out which one to use
    use_backend docker-one \if docker-one

    default_backend docker-router

backend docker-one
    balance roundrobin
    option httpclose
    option forwardfor
    server server1 192.168.0.201:80

backend docker-router
    balance roundrobin
    option httpclose
    option forwardfor
    server server1 192.168.0.200:80

listen stats
    bind *:1936
    mode http
    stats enable
    stats hide-version
    stats realm Haproxy\ Statistics
    stats uri /
    stats auth user:pass
```


example  Dockerfile
```bash
FROM ulsmith/rpi-haproxy
COPY haproxy.cfg /usr/local/etc/haproxy/haproxy.cfg
```


Thats about it, now you should be able to build from your new my-haproxy docker file, pull in rpi-haproxy and update the config. A stats page will be available at port 1936 with login details

username - user
password - pass
