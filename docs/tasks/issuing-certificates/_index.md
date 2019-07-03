---
title: "Issuing Certificates"
linkTitle: "Issuing certificates"
weight: 10
type: "docs"
---

The Certificate resource type is used to request certificates from
different Issuers.

In order to issue any certificates, you'll need to configure an Issuer
resource first.

If you have not configured any issuers yet, you should read the
[Setting up Issuers](../issuers/README.md) guide.

Creating Certificate resources
------------------------------

A Certificate resource specifies fields that are used to generated
certificate signing requests which are then fulfilled by the issuer type
you have referenced.

Certificates specify which issuer they want to obtain the certificate
from by specifying the `certificate.spec.issuerRef` field.

A basic Certificate resource, for the `example.com` and
`www.example.com` DNS names that is valid for 90d and renews 15d before
expiry is below:

``` {.yaml}
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: example-com
  namespace: default
spec:
  secretName: example-com-tls
  duration: 2160h # 90d
  renewBefore: 360h # 15d
  commonName: example.com
  dnsNames:
  - example.com
  - www.example.com
  issuerRef:
    name: ca-issuer
    # We can reference ClusterIssuers by changing the kind here.
    # The default value is Issuer (i.e. a locally namespaced Issuer)
    kind: Issuer
```

The signed certificate will be stored in a Secret resource named
`example-com-tls` once the issuer has successfully issued the requested
certificate.

The Certificate will be issued using the issuer named `ca-issuer` in the
`default` namespace (the same namespace as the Certificate resource).

> **Note**: If you want to create an Issuer that can be referenced by
> Certificate resources in **all** namespaces, you should create a
> [ClusterIssuer](../../reference/clusterissuers) resource and set the
> `certificate.spec.issuerRef.kind` field to `ClusterIssuer`.

> **Note**: The `renewBefore` and `duration` fields must be specified using
> Golang's `time.Time` string format, which does not allow the `d` (days)
> suffix. You must specify these values using `s`, `m` and `h` suffixes
> instead. Failing to do so without installing the
> [webhook](../../install/webhook.md) component can prevent cert-manager from
> functioning correctly ([#1269](https://github.com/jetstack/cert-manager/issues/1269)).

> **Note**: Take care when setting the `renewBefore` field to be very close to
> the `duration` as this can lead to a renewal loop, where the Certificate is
> always in the renewal period. Some Issuers set the `notBefore` field on
> their issued X.509 certificate before the issue time to fix clock-skew
> issues, leading to the working duration of a certificate to be less than
> the full duration of the certificate. For example, Let's Encrypt sets
> it to be one hour before issue time, so the actual *working duration* of
> the certificate is 89 days, 23 hours (the *full duration* remains 90
> days).

A full list of the fields supported on the Certificate resource can be
found in the
[API reference documentation](https://docs.cert-manager.io/en/release-0.8/reference/api-docs/index.html#certificatespec-v1alpha1).

Temporary certificates whilst issuing
-------------------------------------

With some Issuer types, certificates can take a few minutes to be
issued.

A temporary untrusted certificate will be issued whilst this process
takes places if another certificate does not already exist in the target
Secret resource.

This helps to improve compatibility with certain ingress controllers
(e.g. [ingress-gce](https://github.com/kubernetes/ingress-gce)) which
require a TLS certificate to be present at all times in order to
function.

After the real, valid certificate has been obtained, cert-manager will
replace the temporary self signed certificate with the valid one, **but
will retain the same private key**.
