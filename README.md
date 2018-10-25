# NGINX OpenShift Router

This repo includes an implementation of an OpenShift Router based on [NGINX](https://nginx.org) and [NGINX Plus](https://www.nginx.com/products/). 

## What is the Router?

The [Router](https://docs.okd.io/latest/install_config/router/index.html) provides edge load balancing for applications on OpenShift, acting as an entry point for all external requests coming to applications running on OpenShift. The Router’s job is to identify which application a particular request is sent to and then route it to an instance (pod) of that application. OpenShift includes a special resource named [Route](https://docs.okd.io/latest/architecture/networking/routes.html) for configuring the Router.

## NGINX OpenShift Router

NGINX Router is built on top of OpenShift Template Router. Below are the key features of NGINX  Router: 
* **Full support of the Route specification.** NGINX Router fully supports the features defined by the Route resource. 
* **Customization options.** Various customization options and extra features are available through environment variables and Route annotations – a common approach in other Router implementations. 
* **Familiar operational experience.** NGINX Router is integrated in OpenShift through the Template Router software, the same software that underpins the default Router implementation. As a result, you get the familiar operational experience, which makes it easier to migrate from the default Router implementation. 
* **NGINX performance and stability.** With NGINX Router you get the performance and reliability of NGINX software.
* **Latest NGINX features.** We are also excited to bring our new features, such as native support for gRPC load balancing, into the OpenShift Router. As new features are made available in NGINX and NGINX Plus, they can be incorporated into the Router’s capabilities. 
* **Support for TCP/UDP load balancing.** NGINX Router brings support for load balancing TCP/UDP applications, including supporting edge TLS termination and re-encryption for TCP, via a TCP/UDP load balancing [extension](docs/configuration.md/#tcpudp-load-balancing-extension).
* **Support for Prometheus**. NGINX Router can optionally expose metrics ready to be collected by [Prometheus](https://prometheus.io/).
* **Advanced features of NGINX Plus.** When NGINX Router is used with NGINX Plus, you get the additional benefits of NGINX Plus, such as its monitoring API, dashboard and extended number of metrics for Prometheus.

## How To Get Started

* Read the installation guide for [NGINX](docs/nginx-oss-router-install.md) or [NGINX Plus](docs/nginx-plus-router-install.md) installation instructions.
* See how to use NGINX Router for edge load balancing of an example HTTP application in our [Cafe Application example](examples/cafe-app).
* See how to use NGINX Router for edge load balancing of a TCP/UDP application in our [TCP/UDP example](examples/tcp-udp).

## Contacts

We’d like to hear your feedback! If you have any questions or suggestions for NGINX Router, please create an issue on GitHub.
