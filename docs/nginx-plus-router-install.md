# Installing the NGINX Plus Router

## Prerequisites

* Build the Router image and upload it to your private registry. Please see the instructions [here](build-plus-image.md)
* It is important to upload the image to the private registry *before* deleting the default Router, as it will render the registry unavailable.

## 1. Log into the OpenShift Cluster

1. Log in as admin:
    ```
    $ oc login -u system:admin
    ```

1. Select the `default` project:
    ```
    $ oc project default
    ```

## 2. Delete the Default Router

1. Back up the default Router config, in case it needs to be recreated:
    ```
    $ oc get -o yaml service/router dc/router clusterrolebinding/router-router-role serviceaccount/router > default-router-backup.yaml
    ```

1. Delete the Router:
    ```
    $ oc delete -f default-router-backup.yaml
    ```

## 3. Deploy the NGINX Plus Router

1. Deploy the NGINX Plus Router:
    ```
    $ oc adm router router --images=docker-registry.default.svc:5000/openshift/nginx-plus-openshift-router:0.2 --type='' --selector='node-role.kubernetes.io/infra=true'
    ```

    **Note**:
    * The NGINX Plus Router image must be stored in the `openshift` directory, with the name `nginx-plus-openshift-router:0.2`
    * The selector parameter specifies a label selector for nodes where the Router will be deployed: `node-role.kubernetes.io/infra=true`. Use a selector that makes sense for your environment.

1. Run the following command to make sure that the Router pods are running:
    ```
    $ oc get pods
    ```
    You should see a Router pod with the name `router-1-<string>`.

## 4. Access the Live Activity Monitoring Dashboard

1. By default, the NGINX Plus status dashboard is available via port 1936 of the node where the Router is running (you can change this port with the `STATS_PORT` env variable). To access the dashboard outside of the node, you need to add an entry to the IPtables rules for that node:
    ```
    $ sudo iptables -I OS_FIREWALL_ALLOW -p tcp -s <ip range> -m tcp --dport 1936 -j ACCEPT
    ```
1. Open your browser at `http://<node-ip>:1936/dashboard.html` to access the dashboard.

## 5. Support for Prometheus Monitoring

If you are using [Prometheus](https://prometheus.io/), you can deploy the [NGINX Prometheus Exporter](https://github.com/nginxinc/nginx-prometheus-exporter) with the NGINX Plus Router to export the NGINX Plus metrics into Prometheus. To add the exporter to the Router:

1. Patch the Router deployment configuration to add the exporter container to the Router pod:
    ```
    $ oc patch dc/router -p 'spec:
      template:
        spec:
          containers:
          - image: nginx/nginx-prometheus-exporter:0.1.0
            name: nginx-prometheus-exporter
            ports:
            - name: prometheus
              containerPort: 9113
            args:
              - -web.listen-address
              - :9113
              - -nginx.plus
              - -nginx.scrape-uri
              - http://127.0.0.1:1936/api'
    ```
    The exporter will make the metrics available on port 9113.

    **Note**:
    * Change port 1936 in the patch command if the Router was configured to expose the status on a different port other than the default port 1936.

1. Annotate the Router service to indicate to Prometheus to automatically scrape the metrics from the Router endpoints through port 9113:
    ```
    $ oc annotate service router --overwrite prometheus.io/port=9113 prometheus.io/scrape=true
    ```

    **Note**:
    * Your Prometheus must be configured to automatically discover targets through the annotations `prometheus.io/port` and `prometheus.io/scrape` applied to a service.


## Uninstall the NGINX Plus Router

* Delete the NGINX Plus Router:
    ```
    $ oc delete service/router dc/router clusterrolebinding/router-router-role serviceaccount/router
    ```
