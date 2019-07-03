---
title: "Setting up Issuers"
linkTitle: "Configuring Issuers"
weight: 10
type: "docs"
---

Before you can begin issuing certificates, you must configure at least
one Issuer or ClusterIssuer resource in your cluster.

These represent a certificate authority from which signed x509
certificates can be obtained, such as Let's Encrypt, or your own
signing key pair stored in a Kubernetes Secret resource. They are
referenced by Certificate resources in order to request certificates
from them.

An [Issuer](../../reference/issuers.md) is scoped to a single namespace, and
can only fulfill [Certificate](../../reference/certificates.md) resources
within its own namespace. This is useful in a multi-tenant environment where
multiple teams or independent parties operate within a single cluster.

On the other hand, [ClusterIssuer](../../reference/clusterissuers.md) is a
cluster wide version of an [Issuer](../../reference/issuers.md). It is able to
be referenced by [Certificate](../../reference/certificates.md) resources in
any namespace.

Users often create `letsencrypt-staging` and `letsencrypt-prod`
[ClusterIssuers](../../reference/clusterissuers.md) if they operate a
single-tenant environment and want to expose a cluster-wide mechanism for
obtaining TLS certificates from [Let's Encrypt](https://letsencrypt.org).

Supported issuer types
----------------------

cert-manager supports a number of different issuer backends, each with
their own different types of configuration.

Please follow one of the below linked guides to learn how to set up the
issuer types you require:

- **[CA](./setup-ca.md)**: issue certificates signed by a X509 signing keypair,
  stored in a Secret in the Kubernetes API server.
- **[Self signed](./setup-selfsigned.md)**: issue self signed certificates.
- **[ACME](./setup-acme/)**: issue certificates obtained by performing
  challenge validations against an ACME server such as
  [Let's Encrypt](https://letsencrypt.org).
- **[Vault](./setup-vault.md)**: issue certificates from a Vault instance
  configured with the
  [Vault PKI backend](https://www.vaultproject.io/docs/secrets/pki/index.html).
- **[Venafi](./setup-venafi.md)**: issue certificates from a
  [Venafi](https://venafi.com) Cloud or Trust Protection Platform instance.

Additional information
----------------------

There are a few key things to know about Issuers, but for full
information you can refer to the
[Issuer reference docs](../../reference/issuers.md).

### Difference between Issuers and ClusterIssuers

ClusterIssuers are a resource type similar to
[Issuers](../../reference/issuers.md) specified in exactly the same way, but
they do not belong to a single namespace and can be referenced by Certificate
resources from multiple different namespaces.

They are particularly useful when you want to provide the ability to obtain
certificates from a central authority (e.g. Letsencrypt, or your internal CA)
and you run single-tenant clusters.

The resource spec is identical, and you should set the
`certificate.spec.issuerRef.kind` field to ClusterIssuer when creating your
Certificate resources.
