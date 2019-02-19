---
title: Eclipse Kura
---

## Eclipse Kura™ – IoT Gateway

Also see:

  * [https://github.com/ctron/kura-emulator/tree/master/openshift](https://github.com/ctron/kura-emulator/tree/master/openshift) – Containerized version of Kura
  * [https://github.com/eclipse/kura](https://github.com/eclipse/kura) – Eclipse Kura project

### Perform installation

Execute the following commands to deploy Eclipse Kura:

    oc new-project kura --display-name 'Eclipse Kura'
    oc new-app --file=https://raw.githubusercontent.com/ctron/kura-emulator/tutorial-ece2018/openshift/kura-data-persistence.yml -p GIT_BRANCH=tutorial-ece2018

### Register the device with Hono

First of all the Kura instance needs to be registered with Eclipse Hono:

    curl -X POST -H 'Content-Type: application/json' -d '{"device-id": "4711"}' https://hono-service-device-registry-https-hono.[[my.cluster.tld]]/registration/DEFAULT_TENANT

### Connect Kura to Hono

Next we need to connect the Kura gateway to Hono:

  * Go to the Kura Web UI: https://console-kura.[[my.cluster.tld]]
  * Log in with: `admin` / `admin`
  * Navigate to: "Cloud Connections"
  * Select the list entry "Service PID" = "org.eclipse.kura.cloud.CloudService"
  * On the tab "CloudService":
    * `Encode gzip` = `false`
    * `Enable Default Subscriptions` = `false`
    * `Birth Cert Policy` = `Disable publishing`
    * `Payload Encoding` = `Simple JSON`
    * Press the "Apply" button -> Press "Yes"
  * On the tab "DataService":
    * `Connect Auto-on-startup` = `true`
    * `Store Housekeeper-interval` = `30`
    * `Enable Rate Limit` = `false`
    * Press the "Apply" button -> Press "Yes"
  * On the tab "MqttDataTransport"
    * `Broker-url` = `mqtt://hono-adapter-mqtt-vertx.hono.svc:1883/`
    * `Topic Context Account-Name` = `telemetry/DEFAULT_TENANT`
    * `Username` = `sensor1@DEFAULT_TENANT`
    * `Password` = `hono-secret`
    * `Client-id` = `4711`
    * Press the "Apply" button -> Press "Yes"
  * Press the "Connect/Disconnect" button

## Build and deploy

Fetch the `kura-app.yml` file:

    curl -LO {{site.url}}/src/gateway/kura/kura-app.yml

Execute the following commands to set up the pipeline build:

    oc project kura
    oc new-app jenkins-ephemeral
    oc create -f kura-app.yml

Wait until the Jenkins deployment is ready. Then navigate to "Build" -> "Pipelines" in
the OKD web UI. Open the pipeline "kura-app-generator-1" and start the pipeline by
pressing the "Start Pipeline" button.

After the build is complete, you should see a new Kura application named "Generator #1" in the Kura Web UI. As
the Kura Web UI sometimes doesn't refresh, it might be necessary to manually reload the Web UI.
