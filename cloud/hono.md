---
title: Eclipse Hono
---

Also see:

  * [http://enmasse.io/](http://enmasse.io/) – EnMasse
  * [https://eclipse.org/hono](https://eclipse.org/hono) – Eclipse Hono

## Deploy EnMasse

The first step is to deploy EnMasse, as scalable messaging layer. This provides the AMQP 1.0 messaging system
that will be used by Eclipse Hono later on.

### Download

    curl -LO https://github.com/EnMasseProject/enmasse/releases/download/0.26.2/enmasse-0.26.2.tgz
    tar xzf enmasse-0.26.2.tgz

### Deploy

    oc login -u admin
    oc new-project enmasse-infra --display-name='EnMasse'
    oc apply -f enmasse-0.26.2/install/bundles/enmasse-with-standard-authservice

### Check readiness

Using the Web UI:

  * https://[[my.cluster.tld]]:8443
  * Navigate to the project "EnMasse Instance"
  * Check if the deployment `api-server` is ready

Using the command line:

    oc get deploy/api-server deploy/keycloak

Verify that the "AVAILABLE" column shows "1":

    NAME         DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    api-server   1         1         1            1           1m
    keycloak     1         1         1            1           1m

### Switch back to normal user

    oc login -u developer

## Eclipse Hono

The next step makes use of the EnMasse deployment and adds Eclipse Hono on top of that.

### Deploy

    oc new-project hono --display-name='Eclipse Hono'
    
    oc create configmap influxdb-config --from-file=influxdb.conf
    oc label configmap/influxdb-config app=hono-metrics
    
    oc create -f https://raw.githubusercontent.com/eclipse/hono/0.8.x/deploy/src/main/deploy/openshift_s2i/hono-address-space.yml
    oc process -f https://raw.githubusercontent.com/eclipse/hono/0.8.x/deploy/src/main/deploy/openshift_s2i/hono-template.yml -p GIT_BRANCH=0.8.x | oc create -f -
    oc create -f https://raw.githubusercontent.com/eclipse/hono/0.8.x/deploy/src/main/deploy/openshift_s2i/hono-tenant-template.yml
    oc process hono-tenant HONO_TENANT_NAME=DEFAULT_TENANT RESOURCE_NAME=defaulttenant CONSUMER_USER_NAME=consumer CONSUMER_USER_PASSWORD="$(echo -n verysecret | base64)"| oc create -f -

### Deploy metrics dashboard

    oc new-project grafana --display-name='Grafana Dashboard'
    
    oc create configmap grafana-provisioning-dashboards --from-file=grafana/provisioners
    oc create configmap grafana-dashboard-defs --from-file=grafana/dashboards
    oc label configmap grafana-provisioning-dashboards app=hono-metrics
    oc label configmap grafana-dashboard-defs app=hono-metrics
    
    oc process -f https://raw.githubusercontent.com/eclipse/hono/0.8.x/deploy/src/main/deploy/openshift_s2i/grafana-template.yml -p ADMIN_PASSWORD=admin -p GIT_BRANCH=0.8.x | oc create -f -
