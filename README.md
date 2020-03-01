# Kuberty
Kubernetes native testing framework for microservices. Write your tests in any language, declare your service dependencies and Kuberty will build a test cluster and test your service for you.

## Documentation
To learn more about Kuberty go to [our documentation](https://kuberty.io/docs).

## Getting started
### Installation
**Install the Kuberty cli locally:**
```bash
wget https://github.com/kuberty/kuberty/releases/... 
chmod +x kuberty 
mv kuberty /bin/bash/kuberty
```
Installation instructions for [Windows](/docs/installation/windows), [Mac](/docs/installation/mac) and [Docker](/docs/installation/docker).

**Install Kuberty on your cluster:**
```bash
kubectl apply -f https://raw.githubusercontent.com/kuberty/kuberty/master/deploy/install-kuberty.yaml
```

### Example
A simple example of how to test an api gateway that calls a count-service. This example will use the "python" test executor.

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
databases:
- type: redis
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
