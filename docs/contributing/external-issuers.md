---
title: "Implementing External Issuers"
linkTitle: "implementing-external-issuers"
weight: 70
type: "docs"
---

# Implementing External Issuers

cert-manager offers a number of [core issuer types](../configuration/_index.md)
that represent certificate authorities that can sign certificates when
requested. As of v0.11, cert-manager also supports out-of-tree external issuers
natively, and treats them the same as in-tree issuer types. For more information
on how to install and configure external issuer types, read the documentation
[here](../configuration/external.md).

## Concepts

An issuer represents a certificate authority that signs incoming certificate
requests. In cert-manager, the `CertificateRequest` resource represents a single
request for a signed certificate, containing the raw certificate request PEM
data as well as other information that can be used to describe the designed
certificate.

In cert-manager, each issuer type has its own controller that watches these
`CertificateRequest` resources and waits for one to be created which is meant
for itself. This is done by the `issuerRef` stanza on the `CertificateRequest`
which inside contains - name, kind, group. The `group` denotes an API group, for
example `cert-manager.io` which is responsible for all core issuer types. `kind`
denotes the kind resource type of the issuer, such as an `Issuer` or
`ClusterIssuer`. Finally, the `name` denotes the name of the issuer resource
inside of that kind.

When an issuer controller observes a new `CertificateRequest`, it ensure that
the request is meant for its controller type, and if so, then ensures that the
corresponding issuer resource exists in Kubernetes. If these are both true, it
will then use the information inside that issuer resource to attempt to create a
signed certificate, based upon the certificate request.

Once a signed certificate has been gathered by the issuer controller, it then
updates the status of the `CertifiateRequest` resource with the signed
certificate. It is then important to then update the condition status of that
resource to a ready state, as this is what is used to signal to higher order
controllers, such as the `Certificate` controller, that the resource is ready to
be consumed. Conversely, if the `CertificateRequest` fails, it is as important
to mark the resource as such, as this will also be used to signal to higher
order controllers. You can read the valid condition sates
[here](../concepts/certificaterequest.md#Conditions).

## Implementation
