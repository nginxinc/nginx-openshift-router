# How to Build the NGINX Plus Router Image

In this guide we will build the NGINX Plus Router image and upload it to the OpenShift private registry.

## Prerequisites

Before you can build the image, make sure that the following software is installed on your machine:
* [Docker](https://www.docker.com/products/docker)
* [git](https://git-scm.com/)
* NGINX Plus license -- the certificate (`nginx-repo.crt`) and the key (`nginx-repo.key`). If you don't have one, you can sign up for a [free 30-day trial](https://www.nginx.com/free-trial-request/).

**Note**: the instructions assume that you run docker commands on one of the nodes of your OpenShift cluster. To be able to push the image to the `openshift` project, your user account must have admin access to the OpenShift cluster.

## Build the Image

1. Clone the NGINX Router repo and change your directory to `src/nginx-plus`:
    ```
    $ git clone https://github.com/nginxinc/nginx-openshift-router
    $ cd nginx-openshift-router/src/nginx-plus
    ```

1. Make sure that the certificate (`nginx-repo.crt`) and the key (`nginx-repo.key`) of your license are located in the `nginx-plus` directory:
    ```
    $ ls nginx-repo.*
    nginx-repo.crt  nginx-repo.key
    ```

1. Build the image:
    ```
    $ docker build -t nginx-plus-openshift-router:0.2 .
    ```

## Upload the Image to the OpenShift Private Registry

1. To upload the image to your private repository, you need to get login credentials for the private repository. Get those by logging into the registry portal, along with the correct URL syntax for the tag and push commands. Then, log in:
    ```
    $ sudo docker login -p <your key> -e unused -u unused docker-registry-default.router.default.svc.cluster.local
    ```

1. Tag the image:
    ```
    $ docker tag nginx-plus-openshift-router:0.2 docker-registry-default.router.default.svc.cluster.local/openshift/nginx-plus-openshift-router:0.2
    ```

1. Push the image to the registry:
    ```
    $ docker push docker-registry-default.router.default.svc.cluster.local/openshift/nginx-plus-openshift-router:0.2
    ```
