---
title: "Google CloudDNS"
linkTitle: "Google CloudDNS"
weight: 70
type: "docs"
---

This guide explains how to set up an Issuer, or ClusterIssuer, to use
Google CloudDNS to solve DNS01 ACME challenges. It's advised you read the
[DNS01 Challenge Provider](./) page first for a more general
understanding of how cert-manager handles DNS01 challenges.

> **Note**: This guide assumes that your cluster is hosted on Google Cloud and
> that you already have a domain set up with CloudDNS.

Set up a Service Account
------------------------

Cert-manager needs to be able to add records to CloudDNS in order to
solve the DNS01 challenge. To enable this, a GCP service account must be
created with the `dns.admin` role.

> **Note**: For this guide the `gcloud` command will be used to set up the
> service account. Ensure that `gcloud` is in using the correct project and zone
> before entering the commands. These steps could also be completed using
> the Cloud Console.

```shell
gcloud iam service-accounts create dns01-solver \
 --display-name "dns01-solver"

# Replace both uses of project-id with the id of your project
gcloud projects add-iam-policy-binding project-id \
 --member serviceAccount:dns01-solver@project-id.iam.gserviceaccount.com \
 --role roles/dns.admin
```

Create a Service Account Secret
-------------------------------

To access this service account cert-manager uses a key stored in a
Kubernetes Secret. First, create a key for the service account and
download it as JSON file, then create a Secret from this file.

```shell
# Replace use of project-id with the id of your project
gcloud iam service-accounts keys create key.json \
 --iam-account dns01-solver@project-id.iam.gserviceaccount.com

kubectl create secret generic clouddns-dns01-solver-svc-acct \
 --from-file=key.json
```

> **Note**: Keep the key file safe and do not share it, as it could be used to
> gain access to your cloud resources. The key file can be deleted once it has
> been used to generate the Secret.

Create an Issuer That Uses CloudDNS
-----------------------------------

Next, create an Issuer (or ClusterIssuer) with a `clouddns` provider. An
example Issuer manifest can be seen below with annotations.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Issuer
metadata:
  name: example-issuer
spec:
  acme:
    ...
    solvers:
    - dns01:
        clouddns:
          # The ID of the GCP project
          project: my-project-id
          # This is the secret used to access the service account
          serviceAccountSecretRef:
            name: clouddns-dns01-solver-svc-acct
            key: key.json
```

For more information about Issuers and ClusterIssuers, see
[Setting Up Issuers](../../).

Once an Issuer (or ClusterIssuer) has been created successfully a
Certificate can then be added to verify that everything works.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: example-com
  namespace: default
spec:
  secretName: example-com-tls
  issuerRef:
    # The issuer created previously
    name: example-issuer
  commonName: example.com
  dnsNames:
  - example.com
  - www.example.com
```

For more details about Certificates, see
[Issuing Certificates](../../../../issuing-certificates/).
