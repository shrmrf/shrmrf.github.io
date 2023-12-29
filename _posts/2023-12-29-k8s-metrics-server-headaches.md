---
layout: post
title:  "Metrics Server headache"
date:   2023-12-13 20:05:37 +0200
categories: rants
---
One of the pains of playing around with Metrics server is that it sometimes doesn't connect to the node. In my case it was unhealthy and complaining about `x509` certs.

Not for prod but here's a resolution:

### Install Metrics Server

```
kubectl apply -f https://....
```

