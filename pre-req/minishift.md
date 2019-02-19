---
title: Minishift
---

# Setting up Minishift

Citing the Minishift README:

> Minishift is a tool that helps you run OpenShift locally by running a single-node OpenShift cluster inside a VM. You can try out OpenShift or develop with it, day-to-day, on your local host.

This allows you to simply run an OKD cluster on your local machine. This may have a few
limitations, but gets you started rather quickly.

Minishift works by firing up a virtual machine on your local machine, starting OKD inside of it.
I does work on all major operating systems, as long it virtual machines are supported.

For more information see: [https://github.com/minishift/minishift#minishift](https://github.com/minishift/minishift#minishift)

## Typical setup

For a typical setup you need at least 4 CPU cores on your local machine. 40 GB of local disk space, and more than 16 GB of RAM, as we will assign 16 GB of RAM to the virtual machine running OKD:

    minishift start --cpus 4 --memory 16GB --disk-size 40GB

This will start up OKD, and after the startup has finished, you can start the following command
to direct your browswer to the default Web console:

    minishift console --url

## Create a cluster admin

In order to switch to the cluster admin user from your local machine, you will need to enable
the `admin-user` by executing the following command:

    minishift addons apply admin-user

This will create a user named `admin`, with a password of `admin`, that has cluster admin privileges.
