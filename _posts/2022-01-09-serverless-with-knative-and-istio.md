---
layout: post
title:  "Serverless with Knative and Istio on Kubernetes"
author: alin
categories: [ Kubernetes, Serverless, Knative ]
image: https://i.postimg.cc/pLVNkRXS/serverless-knative.png
featured: true
hidden: true
---

**Knative** is an open source project that provides a set of building blocks for creating, deploying, and running serverless applications on Kubernetes. It includes components for building, serving, and scaling containerized applications, and can be used to run a variety of workloads, including microservices, batch jobs, and functions.


**Knative** has several key features that make it useful for building cloud-native applications, including:

- **Automatic scaling**: Knative can automatically scale applications up and down based on demand, making it easy to build highly-available and scalable applications.

- **Serverless**: Knative provides a serverless computing platform, allowing developers to build and deploy applications without having to worry about infrastructure management.

- **Event-driven**: Knative can trigger the execution of an application in response to external events, such as the arrival of new data in a database or the completion of a batch job.

- **Customizable**: Knative provides a set of building blocks that can be customized and extended to suit the specific needs of an application.

**Istio** is a service mesh tools that provides observability, security in depth, and traffic management.

### Prerequirments:
 - Istio ([Install Istio](https://dragomiralin.ro/install-istio-and-octavia-load-balancer))
 - Cert Manager

### Install Knative
Firstly, we have to install Knative

#### Install the Knative Serving component

1. Install the required custom resources by running the command:
```bash
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.8.3/serving-crds.yaml
```
2. Install the core components of Knative Serving by running the command:
```
$ kubectl apply -f https://github.com/knative/serving/releases/download/knative-v1.8.3/serving-core.yaml
```

#### Install a networking layer
We already have Istio installed, so we need to install the Knative Istio controller:
```bash
$ kubectl apply -f https://github.com/knative/net-istio/releases/download/knative-v1.8.1/net-istio.yaml
```

#### Verify the installation
```bash
$ kubectl get pods -n knative-serving
```

#### Configuring domain name
We need to configure domain name to make our domain publicly accessible.

We must update the DNS provider to point to the IP address for our service ingress.
Create a wildcard record for the namespace and custom domain to the ingress IP Address
```
*.knative.example.com                   59     IN     A   <istio-ingress-gateway-ip>
```

Then update `config-domain` so Knative knows about our domain
```bash
$ kubectl edit cm config-domain -n knative-serving
```

```yaml
apiVersion: v1
data:
  knative.example.com: ""
kind: ConfigMap
```

#### Enable TLS
Firstly, we will create a ClusterIssuer for generating certificates

More details about configuring DNS01 Challenge Provider [DNS01](https://cert-manager.io/docs/configuration/acme/dns01/)

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
  namespace: istio-system
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: email@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - dns01:
        cloudflare:
          email: email@example.com
          apiTokenSecretRef:
            name: cloudflare-api-token-secret
            key: api-token
    - http01:
        ingress:
          class: istio
      selector:
        dnsNames:
        - "example.com"
        - "*.example.com"
```
#### Install networking-ns-cert component 
Provisioning a certificate per namespace only works with DNS-01 challenge. 

```bash
$ kubectl apply -f https://github.com/knative/serving/releases/download/v0.21.0/serving-nscert.yaml
```

#### Configure `config-certmanager` ConfigMap 
We have to setup our cluster issuer ref
```bash
$ kubectl edit configmap config-certmanager -n knative-serving
```

Add the issuerRef within the data section:
```yaml
...
data:
...
  issuerRef: |
    kind: ClusterIssuer
    name: letsencrypt-prod
```

#### Turn on Auto TLS 
Update the config-network ConfigMap in the knative-serving namespace to enable autoTLSand specify how HTTP requests are handled

```bash
$ kubectl edit configmap config-network -n knative-serving
```

Add the autoTLS: Enabled attribute under the data section:
```bash
...
data:
...
  autoTLS: Enabled
  httpProtocol: Redirected
...
```

Supported httpProtocol values:

- Enabled: Serve HTTP traffic.
- Disabled: Rejects all HTTP traffic.
- Redirected: Responds to HTTP request with a 302 redirect to ask the clients to use HTTPS.

#### Install Knative CLI
```bash
$ brew install knative/client/kn
```
For other OS check out [Installing the Knative CLI](https://knative.dev/docs/client/install-kn/)

#### Testing
```bash
kn service create helloworld-go --image gcr.io/knative-samples/helloworld-go --env TARGET="World"
```

```bash
$ kn service ls
NAME            URL                                                                 LATEST                AGE   CONDITIONS   READY   REASON
helloworld-go   https://helloworld-go.default.knative.example.com   helloworld-go-00001   15h   3 OK / 3     True    
```

```bash
$ curl https://helloworld-go.default.knative.example.com
Hello World!
```

Knative is a powerful tool for building and deploying cloud-native applications on top of Kubernetes, and can help developers create scalable, event-driven, and serverless applications quickly and easily.