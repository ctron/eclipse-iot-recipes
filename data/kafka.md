---
title: Telemetry ingestion with Apache Kafka
---

Also see: 

  * [http://strimzi.io/](http://strimzi.io/) – Kafka Operator
  * [https://github.com/ctron/hono-example-bridge](https://github.com/ctron/hono-example-bridge) – Forward AMQP 1.0 to Kafka
  * [https://github.com/ctron/hono-example-demo-gauge](https://github.com/ctron/hono-example-demo-gauge) – Demo Gauge

---

**Sed for OSX**

`sed` for OSX works differently than on Linux, the easiest way around it to use the gnu-sed tool:

    brew install gnu-sed

You then need to replace `sed` later on with `gsed`.

---

## Deploy Kafka

### Clone Strimzi

First we need to clone the Strimzi repository:

    git clone https://github.com/strimzi/strimzi-kafka-operator.git -b release-0.10.x

### Deploy Strimzi & create Kafka cluster

    sed -i 's/namespace: .*/namespace: kafka/' strimzi/install/cluster-operator/*RoleBinding*.yaml

    oc login -u admin

    oc new-project kafka  --display-name='Kafka Cluster'
    oc apply -f strimzi/install/cluster-operator
    oc apply -f kafka

    oc login -u developer

## Deploy the Hono Consumer Bridge

Next we need a bridge which forwards AMQP 1.0, coming from Eclipse Hono, to Apache Kafka.

### Deploy Bridge

    oc new-project hono-consumer --display-name 'Hono Consumer'
    oc new-app fabric8/s2i-java~https://github.com/ctron/hono-example-bridge#tutorial-ece2018 -e KAFKA_CLUSTER_NAME=hono-kafka-cluster -e KAFKA_PROJECT=kafka -e AMQP_HOST=messaging-hono-default.enmasse-infra.svc AMQP_USERNAME=consumer AMQP_PASSWORD=verysecret

### Fix up health check & JMX console

    oc set probe dc/hono-example-bridge --readiness --liveness --get-url=http://:8080/health
    oc patch dc/hono-example-bridge --patch '{"spec":{"template":{"spec":{"containers":[{"name":"hono-example-bridge", "ports":[{"containerPort":8778, "name":"jolokia" }]}]}}}}'

## Consume from Kafka

The next step deploys a web frontend, which consumes data from Kafka and displays it in some kind of Web UI gauge.

### Deploy web frontend

    oc new-project demo-gauge --display-name "Demo Gauge"
    oc new-app centos/nodejs-8-centos7~https://github.com/ctron/hono-example-demo-gauge#tutorial-ece2018 -e KAFKA_CLUSTER_NAME=hono-kafka-cluster -e KAFKA_PROJECT=kafka -e PAYLOAD_FORMAT=kura
    oc create route edge --service=hono-example-demo-gauge
