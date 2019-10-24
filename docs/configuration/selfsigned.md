---
title: "SelfSigned"
linkTitle: "SelfSigned"
weight: 30
type: "docs"
---

# SelfSigned Issuer

The `SelfSigned` issuer doesn't represent a certificate authority per se, but
instead denotes that certificates will be signed through "self signing" using
the given private key. This means that the provided private key of the resulting
certificate will be used to sign its own certificate.

This issuer is useful for bootstrapping the CA certificate key pair for some
Private Key Infrastructure (PKI), or for otherwise creating simple certificates.
Clients consuming these certificates have _no way_ to trust this certificate
since there is no CA singer apart from itself and as such would be forced to
trust the certificate as is.

> Note: `CertificateRequests` that reference a self signed certificate _must_
> also contain the annotation `cert-manager.io/private-key-secret-name`. This is
> because without access to the private key of the certificate request, the
> Certificate Request will be unable to self sign the certificate. This
> annotation is added automatically by the `Certificate` controller.

## Deployment

Since the `SelfSigned` issuer requires no dependency on any other resource to be
configured, it is the simplest to configure. All that is required is for the
`SelfSigned` stanza to be present in the issuers spec.

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: selfsigning-issuer
  namespace: sandbox
spec:
  selfSigned: {}
```

Once deployed, you should be able to see immediately that the issuer is ready
for signing. Replace `issuers` here with `clusterissuers` if that is what has
been deployed.

TODO (@joshvanl): add output to this command once the ready status has been
```bash
$ kubectl get issuers selfsigned-issuer -n sandbox -o wide
```

Certificates are now ready to be requested by using the SelfSigned issuer named
`selfsigned-issuer` within the `sandbox` namespace.
