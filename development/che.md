---
title: Eclipse Che
---

This section describes how to deploy Eclipse Che into the IoT demo cluster,
so that you can use the cloud IDE to modify the code, running alongside with
the other components in the cluster.

The following steps will import source code for editing. You may directly use the references
repositories, but this will not allow you to push changes you made. However you can
fork the referenced repositories, and use those forked repositories instead. Just be
sure to align this with the other deployment steps (e.g. like the Kura generator application), so
that you will actually build from this forked repository when you push changes.

## Installing Eclipse Che

The first step is to deploy Eclipse Che to the cluster. The following instructions will
deploy Eclipse Che, in multi-user mode. Keycloak is the backend for the user database.

If you choose not to use TLS, then you need to use `http` instead of `https`, and `ws`
instead of `wss` in the following steps. You will also need to skip the step deploying the
https settings (`oc apply -f https`). 

### Deploy

Execute the following commands, be sure to replace `<initial-admin-password>`:

    cd che/deploy/openshift/templates
    
    oc new-project che --display-name='Eclipse Che'
    oc new-app -f multi/postgres-template.yaml -p CHE_VERSION=6.17.1
    oc new-app -f multi/keycloak-template.yaml -p KEYCLOAK_PASSWORD=<initial-admin-password> -p ROUTING_SUFFIX=[[my.cluster.tld]] -p PROTOCOL=https
    oc apply -f pvc/che-server-pvc.yaml
    oc new-app -f che-server-template.yaml -p CHE_VERSION=6.17.1 -p ROUTING_SUFFIX=[[my.cluster.tld]] -p CHE_MULTIUSER=true -p PROTOCOL=https -p WS_PROTOCOL=wss -p TLS=true
    oc set env dc/che CHE_MULTIUSER=true
    oc set volume dc/che --add -m /data --name=che-data-volume --claim-name=che-data-volume
    oc apply -f https
    
    cd ../../../..

### First time login

  * Go to: https://che-che.[[my.cluster.tld]]
  * Register a new user

## Import Eclipse Kura example code

If you deployed the Eclipse Kura example to your cluster, you can also import the example
code in an Eclipse Che workspace:

### Create a new workspace

  * Select the "Java CentOS" image, search in "Single Machine" category
  * Enter "kura-example" as a "Name" of the workspace
  * Do not add any projects!
  * Press "Create & Proceed editing" (in the dropdown list)
  * Press "Edit"
  * Switch to "Installers"
  * Enable "Git credentials"
  * Press "Save" (bottom of the page)
  * Press "Open" (top of the page)

### Configure workspace

  * Open "Profile" -> "Preferences"
  * Switch to "Git / Committer"
    * Enter name and e-mail
  * Switch to "SSH / VCS"
    * Press "Generate Key"
    * Enter `github.com`
    * Press "View" in column "Public Key"
    * Copy "Public SSH Key"
    * Add the key to your GitHub account

### Import project

  * Select "Import project…"
  * Use "Git" (not "Git Hub")
    * URL: git@github.com:ctron/kura-examples.git
    * Enable "Import recursively"
    * Tick the checkbox on "Branch"
    * And enter the following branch: `tutorial-ece2018`
    * Press "Import"
  * In the project configuration dialog which pops up
    * Select "Java / Maven"
    * Press "Save"

## Setting up build triggers

When you make changes to those projects, you might want to set up build triggers, so that a pushed
changes starts a new build inside the OKD cluster, and creates, and deploys a new image:

### Getting webhook information

You can extract the webhook information using the following steps.

Switch to the project:

    oc project <project>

Get all build configurations:

    oc get bc

Which should give a list like:

    NAME                      TYPE      FROM      LATEST
    hono-example-demo-gauge   Source    Git       1

Then get the web hook settings:

    oc describe bc hono-example-demo-gauge

Look for the section "Webhook GitHub":

    …
    Webhook GitHub:
    	URL:	https://[[my.cluster.tld]]:8443/apis/build.openshift.io/v1/namespaces/demo-gauge/buildconfigs/hono-example-demo-gauge/webhooks/<secret>/github
    …

And extract the GitHub webhook secret, use the output of this command to replace the `<secret>` placeholder above:

    oc get bc hono-example-demo-gauge -o jsonpath --template '{..github.secret}'

### Setting a build trigger

**Note:** This step is optional! Don't do it if the trigger already exists

If a build configuration is missing a build trigger, a new one can be added with the following command:

    oc set triggers  bc <build-config> --from-github

### Adding webhooks in GitHub

For each build config you need to perform the following steps:

  * Extract the webhook information as described above
  * Go to your GitHub project fork
  * Switch to "Settings" -> "Webhooks"
  * Click on "Add webhook"
    * Enter the Payload URL
    * Set the "Content Type" to `application/json`
    * Leave the "secret" empty
    * Leave the SSL verification enabled (unless you don't use proper TLS)
    * Press "Add webhook"
  * Verify the webhook by opening it again and check the log at the bottom of the page
