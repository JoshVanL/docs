---
title: "Akamai FastDNS"
linkTitle: "Akamai FastDNS"
weight: 30
type: "docs"
---

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
        akamai:
          serviceConsumerDomain: akab-tho6xie2aiteip8p-poith5aej0ughaba.luna.akamaiapis.net
          clientTokenSecretRef:
            name: akamai-dns
            key: clientToken
          clientSecretSecretRef:
            name: akamai-dns
            key: clientSecret
          accessTokenSecretRef:
            name: akamai-dns
            key: accessToken
```
