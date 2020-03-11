# Kuberty
<img src="https://github.com/kuberty/kuberty/raw/master/logos/logo.png" width="100">

Kubernetes native testing framework for microservices. Write your tests in any language, declare your service dependencies and Kuberty will build a test cluster and test your service for you.


## Documentation
To learn more about Kuberty go to [our documentation](https://wiki.tcbv.be/en/ict/kubernetes/kuberty).

## Installation
**Install the Kuberty cli locally:**

```bash
wget https://github.com/kuberty/kuberty/releases/...  && chmod +x kuberty  && mv kuberty /bin/bash/kuberty
```

Installation instructions for [Windows](/docs/installation/windows), [Mac](/docs/installation/mac) and [Docker](/docs/installation/docker).

**Install Kuberty on your cluster:**
```bash
kubectl apply -f https://raw.githubusercontent.com/kuberty/kuberty/master/deploy/install-kuberty.yaml
```

### Custom Resource Definitions
**Applications** specifies your application and it's dependencies

```yaml
apiVersion: kuberty.io/v1
kind: Application
metadata:
  name: auth-service-test-deployment-{{ .Values.test_id }}
  namespace: kuberty
  labels:
    app: auth-service
spec:
  app:
    chart: 3000 bytes
    path: helm
  database:
    type: redis
    service:
      name: redis
      namespace: auth-service
  dependencies:
  - app: count-service
    repository: https://github.com/kuberty/example-count-service.git
    revision: v0.0.1
    path: helm
```
**Files** define the artifacts for your tests
```yaml
apiVersion: kuberty.io/v1
kind: File
metadata:
  name: auth-service-tests-file-{{ .Values.file_digest }}
  namespace: Kuberty
spec:
  type: http
  url: https://raw.githubusercontent.com/kuberty/auth-/prometheus/release-2.16/documentation/examples/prometheus.yml
  digest: sha256:4377ad582f1aa7002791e0643f9b2e260b8b015e002d6dc915bf3e4b10927f9e
```
**Tests** define the tests you wish to run

```yaml
apiVersion: kuberty.io/v1
kind: Test
metadata:
  name: auth-service-test-{{ .Values.test_id }}
  namespace: kuberty
  labels:
    test_id: {{ .Values.test_id }}
spec:
  source:
    file: auth-service-tests-file-{{ .Values.file_digest }}
    revision: {{ .Values.revision }}
    path: tests
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

## Interesting projects:
| Project | Relevance | Stars | 
| -- | -- | -- |
| [kubernetes/kube-infra kubetest](https://github.com/kubernetes/test-infra/blob/master/kubetest/README.md) | Kube-test is the way Kubernetes spins up k8s clusters in their e2e testing framework, this has Google Cloud as the only provider for the cluster, which is an issue if we want to test locally. It could be used as one of the bootstrapping frameworks though. | 1.9k
| [srossross/k8s-test-controller](https://srossross.github.io/k8s-test-controller/) | Very simple jobs you can run in a bootstrapped cluster to test functionality. | 17 |

## Interesting articles:
| Article | Relevance |
| ------- | --------- |
| [Kubernetes article on using the e2e framework](https://kubernetes.io/blog/2019/03/22/kubernetes-end-to-end-testing-for-everyone/) | 
