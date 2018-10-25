# How to Build the NGINX Router Image

In this guide we will build the NGINX Router image and upload it to the OpenShift private registry.

## Prerequisites

Before you can build the image, make sure that the following software is installed on your machine:
* [Docker](https://www.docker.com/products/docker)
* [git](https://git-scm.com/)

**Note**: the instructions assume that you run docker commands on one of the nodes of your OpenShift cluster. To be able to push the image to the `openshift` project, your user account must have admin access to the OpenShift cluster.

## Build the Image

1. Clone the NGINX Router repo and change your directory to `src/nginx`:
    ```
    $ git clone https://github.com/nginxinc/nginx-openshift-router
    $ cd nginx-openshift-router/src/nginx
    ```

1. Build the image:
    ```
    $ docker build -t nginx-openshift-router:0.2 .
    ```

## Upload the Image to the OpenShift Private Registry

1. To upload the image to your private repository, you need to get login credentials for the private repository. Get those by logging into the registry portal, along with the correct URL syntax for the tag and push commands. Then, log in:
    ```
    $ sudo docker login -p <your key> -e unused -u unused docker-registry-default.router.default.svc.cluster.local
    ```

1. Tag the image:
    ```
    $ docker tag nginx-openshift-router:0.2 docker-registry-default.router.default.svc.cluster.local/openshift/nginx-openshift-router:0.2
    ```

1. Push the image to the registry:
    ```
    $ docker push docker-registry-default.router.default.svc.cluster.local/openshift/nginx-openshift-router:0.2
    ```
