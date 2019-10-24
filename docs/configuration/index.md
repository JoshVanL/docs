---
title: "Configuration"
linkTitle: "Configuration"
weight: 30
type: "docs"
---

In order to configure cert-manager to begin issuing certificates, first
`Issuer` or `ClusterIssuer` resources must be created. These resources represent
a particular signing authority and detail how the certificate requests are going
to be honoured. You can read more on the concept of issuers
[here](../concepts/issuers.md).

cert-manager supports multiple issuer 'in-tree' types that are donated by being
in the `cert-manager.io` group. cert-manager also supports external issuers than
can be installed into your cluster that belong to other groups. These external
issuer types behave no different and are treated equal to in tree issuer types.

## Supported Issuer Types

cert-manager supports multiple issuer types as follows:

- [CA](./ca.md): Certificate Authority based upon an owned certificate key pair
  for signing.
- [SelfSigned](./selfsigned.md): Sign certificates based upon there own private
  key.
- [Vault](./vault.md): Use the [Vault](https://www.vaultproject.io) server as a
  signing Certificate Authority.
- [Venafi](./venafi.md): Request certificates from the enterprise Certificate
  Authority [Venafi](https://www.venafi.com).
- [ACME](./acme/index.md): Request certificates from an ACME provider such as [Let's
  Encrypt](https://letsencrypt.org).
- [External Issuer](./external.md): Install and request certificates from an
  issuer type not offered by cert-manager by default.
