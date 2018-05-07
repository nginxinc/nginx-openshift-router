# Installing the NGINX Plus Router

## Prerequisites 

* Build the Router image and upload it to your private registry. Please see the instructions [here](build-image.md)

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
    $ oc adm router -o yaml >> default-router-backup.yaml
    ```

1. Delete the Router:
    ```
    $ oc delete -f default-router-backup.yaml
    ```

## 3. Deploy the NGINX Plus Router

1. Deploy the NGINX Plus Router:
    ```
    $ oc adm router router --images=docker-registry.default.svc:5000/openshift/nginx-plus-router:0.1 --type='' --selector='region=infra'
    ```

    **Note**: 
    * The NGINX Plus Router image must be stored in the `openshift` directory, with the name `nginx-plus-router:0.1`
    * The selector parameter specifies a label selector for nodes where the Router will be deployed: `region=infra`. Use a selector that makes sense for your environment.

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

## Uninstall the NGINX Plus Router

* Back up and delete the NGINX Plus Router:
    ```
    $ oc adm router -o yaml >> nginx-plus-router-backup.yaml
    $ oc delete -f nginx-plus-router-backup.yaml
    ```
