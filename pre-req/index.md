---
title: Pre-requisites
---

## OKD Cluster

An OKD cluster version 3.11, to which you have cluster admin privileges
 * Setup [Minishift](minishift.html)
 * Deploy on a real, "all-in-one-master"
     * Deploying on [Hetzner Cloud](hetzner-cloud.html)

## oc binary

You will need the `oc` binary in your `$PATH`.
Download the `openshift-origin-client-tools` from [https://github.com/openshift/origin/releases](https://github.com/openshift/origin/releases) and put it somewhere in your `$PATH`, e.g. under `/usr/local/bin`.

## Linux command line tools

You will need the "usual suspects" of Linux command line tools, like `curl`, `tar`, `cp`, …
 * The easiest way is to actually use Linux
 * Mac OS does most of the things the same way
 * Windows does provide you with a Linux subsystem, and a bunch of Linux command line tools