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

This example will use the "python" test executor (Job and Golang are also available).

test.yaml file for the api gateway:
```yaml
name: api-tests
version: v0.0.1
app:
  path: templates
dependencies:
- name: count-service
  repository: https://github.com/kuberty/example-count-service.git
  revision: v0.0.1
folder: tests
tests:
- name: setup
  file: setup.py
  children:
  - name: get-test
    file: get-test.py
  - name: add-test
    file: add-test.py
    children:
    - name: get-after-add-test
      file: get-after-add-test.py
  - name: subtract-test
    file: subtract-test.py
    children:
    - name: get-after-subtract-test
      file: get-after-subtract-test.py
```

test.yaml file for the count-service:

```yaml
name: count-tests
version: v0.0.1
app:
  path: templates
  environment:
  - key: REDIS_URI
    value: redis://redis.count-service
database:
  type: redis
  user: admin
  service:
    name: redis
    namespace: count-service
#tests:
#we could test the count service as well (these tests would not run in this example, as we assume they have been ran already)
```

When you head into the folder for test.yaml of api-test and run:
```
kuberty test
```
It will execute your tests as follows:

0. Start count-service with clean database, then start api-tests
1. run setup
2. run get-test and roll back to setup state
3. run add-test
4. run get-after-add-test and roll back to setup state
5. run subtract-test
6. run get-after-subtract-test
