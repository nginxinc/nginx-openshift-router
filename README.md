# NGINX Plus OpenShift Router

This repo provides an implementation of an OpenShift Router based on [NGINX Plus](https://www.nginx.com/products/). 

**Note**: An implementation based on [NGINX](https://nginx.org) is part of the [OpenShift Origin repo]().

## What is the Router?

The [Router](https://docs.openshift.org/latest/install_config/router/index.html) provides edge load balancing for applications on OpenShift, acting as an entry point for all external requests coming to applications running on OpenShift. The Router’s job is to identify which application a particular request is sent to and then route it to an instance (pod) of that application. OpenShift includes a special resource named [Route](https://docs.openshift.org/latest/architecture/networking/routes.html) for configuring the Router.

## NGINX Plus Router

NGINX Plus Router is built on top of OpenShift Template Router. Below are the key features of NGINX Plus Router: 
* **Full support of the Route specification.** NGINX Plus Router fully supports the features defined by the Route resource. 
* **Advanced features.** With the NGINX Plus Router, you get the additional benefits of NGINX Plus, such as its monitoring API and dashboard.
* **Customization options.** Various customization options and extra features are available through environment variables and Route annotations – a common approach in other Router implementations. 
* **Familiar operational experience.** NGINX Plus Router is integrated in OpenShift through the Template Router software, the same software that underpins the default Router implementation. As a result, you get the familiar operational experience, which makes it easier to migrate from the default Router implementation. 
* **NGINX Plus performance and stability.** With NGINX Plus Router you get the performance and reliability of NGINX Plus software.
* **Latest NGINX Plus features.** We are also excited to bring our new features, such as native support for gRPC load balancing, into the OpenShift Router. As new features are made available in NGINX Plus, they can be incorporated into the Router’s capabilities. 
* **Support for TCP/UDP load balancing.** NGINX Plus Router brings support for load balancing TCP/UDP applications, including supporting edge TLS termination and re-encryption for TCP, via a TCP/UDP load balancing [extension](docs/configuration.md/#tcpudp-load-balancing-extension).

## How To Get Started

* Read the [installation guide](docs/nginx-plus-router-install.md) for installation instructions.
* See how to use NGINX Plus Router for edge load balancing of an example HTTP application in our [Cafe Application example](examples/cafe-app).
* See how to use NGINX Plus Router for edge load balancing of a TCP/UDP application in our TCP/UDP [example](examples/tcp-udp).

## Contacts

We’d like to hear your feedback! If you have any questions or suggestions for NGINX Plus Router, please create an issue on GitHub.
