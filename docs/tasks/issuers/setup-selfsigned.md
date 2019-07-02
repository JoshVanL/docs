---
title: "Setting up self signing Issuers"
linkTitle: "Self signing"
weight: 30
type: "docs"
---

Self signed Issuers will issue self signed certificates.

This is useful when building PKI within Kubernetes, or as a means to
generate a root CA for use with the [CA Issuer](./setup-ca.md).

A self-signed Issuer contains no additional configuration fields, and
can be created with a resource like so:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: selfsigning-issuer
spec:
  selfSigned: {}
```

> **Note**: The presence of the `selfSigned: {}` line is enough to indicate
> that this Issuer is of type 'self signed'.

Once created, you should be able to issue certificates like usual by
referencing the newly created Issuer in your `issuerRef`:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: example-crt
spec:
  secretName: my-selfsigned-cert
  commonName: "my-selfsigned-root-ca"
  isCA: true
  issuerRef:
    name: selfsigning-issuer
    kind: ClusterIssuer
```
