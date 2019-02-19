---
title: Hetzner Cloud
---

## Deploying OKD on the Hetzner Cloud

**Disclaimer**: This is a personal effort of deploying OKD on top of the Hetzner Cloud offering. This isn't in any way endorsed or officially supported by anyone.

## Registering an account

The first thing you need is to create an account at [Hetzner Cloud](https://console.hetzner.cloud),
and create a new project. Create an access token to the project, and set up the `hcloud` command line tools which you can download from: [https://github.com/hetznercloud/cli/releases](https://github.com/hetznercloud/cli/releases)

Create a new "context" using `hcloud` using the API token. Also see: [https://github.com/ctron/hcloud-okd-setup/#hetzner-cloud](https://github.com/ctron/hcloud-okd-setup/#hetzner-cloud)

## Create a new cluster

Next you can use the scripts from [ctron/hcloud-okd-setup](https://github.com/ctron/hcloud-okd-setup/)
to deploy a new, "all-in-one-master". You will need to create the `config` file first, as the
documentation in the repository describes.

The server type you choose depends on the amount of a applications you want to deploy to this setup. A
reasonable choice is to go for the 32GB RAM machine, using SSDs, but without using dedicated CPUs.

Be sure to set the config setting `ADMIN_PASSWORD`, as otherwhise no cluster admin user will be created.
This is however required for some of the deployment steps.

## TLS with Let's Encrypt

If you want, you can go the extra mile and setup Let's Encrypt and DNS in combination with the
cluster. Which allows you to properly use TLS.
