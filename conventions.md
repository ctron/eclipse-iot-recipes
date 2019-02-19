---
title: Conventions
---

## Executing commands

All commands to be executed are considered to be executed on your local machine.

## Users

By default the user `developer` will be used on the OKD cluster.

If the cluster admin user is required, the documentation will state so and suggest to use the
command `oc login -u admin`, as the recipes assume that you have an admin user named `admin`.

When the cluster admin is no longer needed the command `oc login -u developer` will mark the end.

## Hostnames

The primary DNS of the cluster is assumed to be `my.cluster.tld`, of course that is only a placeholder
for whatever your cluster name is. Something that cluster name will be used as a suffix only, e.g.
`app1.my.cluster.tld`. This would require you to replace `my.cluster.tld`, but keep the `app1.`
host part. In order to make this more obvious, hostnames will be written in the format
of: `app1.[[my.cluster.tld]]`. It is your responsibility to actually replace this, even in config files.

## TLS

These recipes assume that you have set up your cluster with proper TLS. Sometimes, especially for local only testing,
this may not be possible. In this case you may need to "ignore" certificate warnings.

However, this guide should be "secure by default", at least a little bit, and so it should not provide
command by default with insecure settings, like e.g. `curl -k`, …

