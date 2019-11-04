---
title: "Upgrading"
linkTitle: "upgrading"
weight: 10
type: "docs"
---

# Upgrading cert-manager

This section contains information on upgrading cert-manager. It also contains
documents detailing breaking changes between cert-manager versions, and
information on things to look out for when upgrading.

> Note: Before performing upgrades of cert-manager, it is advised to take a backup
> of all your cert-manager resources just in case an issue occurs whilst
> upgrading. You can read how to backup and restore cert-manager in the
> [backup guide](../tutorials/backup.md).

## Upgrading with Helm

If you installed cert-manager using Helm, you can easily upgrade using the Helm
CLI.

> Note: Before upgrading, please read the relevant [instructions at the links
> below](./_index.md#Specific_Version_Upgrade_Tasks) for your from and to
> version.

Once you have read the relevant upgrading notes and taken any appropriate
actions, you can begin the upgrade process like so - replacing `<release_name>`
with the name of your Helm release for cert-manager (usually this is
`cert-manager`) and replacing `<version>` with the version number you want to
install:

Install the cert-manager CustomResourceDefinition resources before upgrading the
Helm chart.
```bash
$ kubectl apply \
     --validate=false \
     -f https://raw.githubusercontent.com/jetstack/cert-manager/<version>/deploy/manifests/00-crds.yaml
```

Add the Jetstack Helm repository if you haven't already.
```bash
$ helm repo add jetstack https://charts.jetstack.io
```

Ensure the local Helm chart repository cache is up to date.
```bash
$ helm repo update
$ helm upgrade --version <version> <release_name> jetstack/cert-manager
```

This will upgrade you to the latest version of cert-manager, as listed in the
[Jetstack Helm chart repository](https://hub.helm.sh/charts/jetstack).

> Note: You can find out your release name using `helm list | grep
> cert-manager`.

## Upgrading using static manifests

If you installed cert-manager using the static deployment manifests published on
each release, you can upgrade them in a similar way to how you first installed
them.

> Note: Before upgrading, please read the relevant instructions at the links
> below for your from and to version.

Once you have read the relevant notes and taken any appropriate actions, you can
begin the upgrade process like so - replacing `<version>` with the version
number you want to install:

```bash
$ kubectl apply \
       --validate=false \
       -f https://github.com/jetstack/cert-manager/releases/download/<version>/cert-manager.yaml
```

> Note: If you are running Kubernetes v1.15 or below, you will need to add the
> `--validate=false` flag to your `kubectl apply` command above else you will
> receive a validation error relating to the
> `x-kubernetes-preserve-unknown-fields` field in our `CustomResourceDefinition`
> resources.  This is a benign error and occurs due to the way `kubectl`
> performs resource validation.


## Specific Version Upgrade Tasks

- [upgrading-0.2-0.3](./upgrading-0.2-0.3.md)
- [upgrading-0.3-0.4](./upgrading-0.3-0.4.md)
- [upgrading-0.4-0.5](./upgrading-0.4-0.5.md)
- [upgrading-0.5-0.6](./upgrading-0.5-0.6.md)
- [upgrading-0.6-0.7](./upgrading-0.6-0.7.md)
- [upgrading-0.7-0.8](./upgrading-0.7-0.8.md)
- [upgrading-0.8-0.9](./upgrading-0.8-0.9.md)
- [upgrading-0.9-0.10](./upgrading-0.9-0.10.md)
- [upgrading-0.10-0.11](./upgrading-0.10-0.11.md)
