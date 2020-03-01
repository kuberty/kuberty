# kuberty
Kubernetes native microservice testing framework.

## Getting started
### Installation
Install the kuberty cli:
```bash
wget https://github.com/kuberty/kuberty/releases/... 
chmod +x kuberty 
mv kuberty /bin/bash/kuberty
```
Installation instructions for Windows, mac and Docker.

Install the kuberty server (and istio) on your kubernetes cluster:
```bash
kubectl apply -f https://raw.githubusercontent.com/kuberty/kuberty/master/deploy/install-kuberty.yaml
```

### Hello kuberty
This simple example consists of an api gateway (hat calls a count service (/increment?amount={name} returns {"count": <number of xalls}>). We won't go into detail on how to setup the services, the source is available on the links.

test.yaml file for the gateway:
```yaml
name: api-tests
version: v0.0.1
app:
- 
dependencies:
- name: count-service
  repository: https://github.com/kuberty/example-count-service.git

revision: v0.0.1
 

