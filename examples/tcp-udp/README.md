# TCP/UDP Load Balancing Example

In this example we deploy the NGINX Plus OpenShift Router, a DNS server and then configure both TCP and UDP load balancing for the DNS server using routes.

## Prerequisites

* We use `dig` for testing. Make sure it is installed on your machine.
* Make sure to open port 5353 in the firewall for both TCP and UDP traffic on the node where the Router is running:
    ```
    $ sudo iptables -I OS_FIREWALL_ALLOW -p tcp --dport 5353 -j ACCEPT
    $ sudo iptables -I OS_FIREWALL_ALLOW -p udp --dport 5353 -j ACCEPT
    ```

## 1. Deploy the NGINX Plus Router

1. Follow the installation instructions for [NGINX Plus](../../docs/nginx-plus-router-install.md).

1. Save the public IP address of the node where the Router is running into a shell variable:
    ```
    $ export ROUTER_IP=xxx.xxx.xxx.xxx
    ```
1. Save port 5353 of the Router into a shell variable:
    ```
    $ export ROUTER_5353_PORT=5353
    ```

### 2. Deploy the DNS Server

We deploy two replicas of [CoreDNS](https://coredns.io/), configured to forward DNS queries to `8.8.8.8`.

Deploy the DNS server:

```
$ kubectl apply -f dns.yaml
```

## 3. Create the Routes

```
$ oc create -f dns-udp-route.yaml
$ oc create -f dns-tcp-route.yaml
```

### 3. Test the DNS Server

To test that the configured TCP/UDP load balancing works, we resolve the name `openshift.io` using our DNS server available through the Router:

1. Resolve `openshift.io` through UDP:
    ```
    $ dig @$ROUTER_IP -p $ROUTER_5353_PORT openshift.io

    ; <<>> DiG 9.10.6 <<>> @192.168.99.100 -p 5353 openshift.io
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 34229
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;openshift.io.			IN	A

    ;; ANSWER SECTION:
    openshift.io.		6	IN	A	52.44.3.11
    openshift.io.		6	IN	A	52.71.98.175

    ;; Query time: 47 msec
    ;; SERVER: 192.168.99.100#5353(192.168.99.100)
    ;; WHEN: Fri Aug 17 12:21:42 PDT 2018
    ;; MSG SIZE  rcvd: 97
    ```
    
1. Resolve `openshift.io` through TCP:
    ```
    $ dig @$ROUTER_IP -p $ROUTER_5353_PORT openshift.io +tcp

    ; <<>> DiG 9.10.6 <<>> @192.168.99.100 -p 5353 openshift.io +tcp
    ; (1 server found)
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 24550
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 512
    ;; QUESTION SECTION:
    ;openshift.io.			IN	A

    ;; ANSWER SECTION:
    openshift.io.		7	IN	A	52.44.3.11
    openshift.io.		7	IN	A	52.71.98.175

    ;; Query time: 62 msec
    ;; SERVER: 192.168.99.100#5353(192.168.99.100)
    ;; WHEN: Fri Aug 17 12:22:12 PDT 2018
    ;; MSG SIZE  rcvd: 97
    ```
