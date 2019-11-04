---
title: "DNS01"
linkTitle: "DNS01"
weight: 30
type: "docs"
---

# Configuring DNS01 Challenge Provider

This page contains details on the different options available on the `Issuer`
resource's DNS01 challenge solver configuration.

For more information on configuring ACME issuers and their API format, read the
[ACME Issuers](<../index.md) documentation.

DNS01 provider configuration must be specified on the Issuer resource, similar
to the examples in the setting up documentation:

You can read about how the DNS01 challenge type works on the [Let's Encrypt
challenge types
page](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge).

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: example-issuer
spec:
  acme:
    email: user@example.com
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: example-issuer-account-key
    solvers:
    - dns01:
        clouddns:
          project: my-project
          serviceAccountSecretRef:
            name: prod-clouddns-svc-acct-secret
            key: service-account.json
```

Each issuer can specify multiple different DNS01 challenge providers, and
it is also possible to have multiple instances of the same DNS provider on a
single Issuer (e.g. two clouddns accounts could be set, each with their own
name).

For more information on utilising multiple solver types on a single Issuer,
read the multiple-solver-types section.

## Setting nameservers for DNS01 self check

cert-manager will check the correct DNS records exist before attempting a DNS01
challenge.  By default, the DNS servers for this check will be taken from
`/etc/resolv.conf`.  If this is not desired (for example with multiple
authoritative nameservers or split-horizon DNS), the cert-manager controller
exposes a flag that allows you alter this behaviour:

Example usage::
```bash
--dns01-recursive-nameservers "8.8.8.8:53,1.1.1.1:53"
```

If you're using the `cert-manager` helm chart, you can set recursive nameservers
through `.Values.extraArgs` or at the command at helm install/upgrade time
with `--set`:

```bash
--set 'extraArgs={--dns01-recursive-nameservers=8.8.8.8:53\,1.1.1.1:53}'
```

## Delegated Domains for DNS01

By default, cert-manager will not follow CNAME records pointing to subdomains.

If granting cert-manager access to the root DNS zone is not desired, then the
`_acme-challenge.example.com` subdomain can instead be delegated to some other,
less privileged domain.
Once a CNAME record has been configured to point at the desired domain, and the
DNS configuration/credentials for the zone that *should be updated* have been
provided, all that is left to be done is adding an additional field into the
relevant `dns01` solver:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  ...
spec:
  acme:
    ...
    solvers:
    - dns01:
        # Valid values are None and Follow
        cnameStrategy: Follow
        clouddns:
          ...
```

cert-manager will then follow CNAME records recursively in order to determine
which DNS zone to update during DNS01 challenges.


## Supported DNS01 providers

A number of different DNS providers are supported for the ACME issuer. Below is
a listing of available providers, their `.yaml` configurations, along with additional Kubernetes
and provider specific notes regarding their usage.

- [acme-dns](./acme-dns.md)
- [akamai](./akami.md)
- [azuredns](./azuredns.md)
- [cloudflare](./cloudflare.md)
- [google](./google.md)
- [route53](./route53.md)
- [digitalocean](./digitalocean.md)
- [rfc2136](./rfc2136.md)

## Webhook

cert-manager also supports out of tree DNS providers using an external webhook.
Links to these supported providers along with their documentation are below:
- [alidns-webhook](https://github.com/pragkent/alidns-webhook)
- [cert-manager-webhook-dnspod](https://github.com/qqshfox/cert-manager-webhook-dnspod)
- [cert-manager-webhook-selectel](https://github.com/selectel/cert-manager-webhook-selectel)
- [cert-manager-webhook-softlayer](https://github.com/cgroschupp/cert-manager-webhook-softlayer)

To create a new unsupported DNS provider, follow the development documentation
[here](../../../devel/dns-providers.md).

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: example-issuer
spec:
  acme:
   ...
    solvers:
    - dns01:
        webhook:
          groupName: <webhook-group-name>
          solverName: <webhook-solver-name>
          config:
            ...
            <webhook-specific-configuration>
```
