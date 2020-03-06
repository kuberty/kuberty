# Controller Testing
Another feature Kuberty should provide is the ability to test CRD controllers.

Users should be able to write Test files within the controller source itself to test kubernetes-related behavior.


## Use case example
I want to make sure that my informers (the code that passes changes of the api resources to my controller) are passing information properly.

Let's assume the controller is written in go.

Our CI server will do the following:
```bash
cd $TESTING_DIRECTORY
go test -c -o test_binary
# Upload the test_binary as a kuberty.io/v1/File
# Upload the test file defined below
kuberty test create controller-test -s test_binary -t golang # CLI way of doing these two things
# Watch the API until the test status is returned
kuberty test wait controller-test
```

Now our test is as simple as follows:
```yaml
apiVersion: kuberty.io/v1
kind: Test
metadata:
  name: controller-test-{{ .Values.test_id }}
  namespace: kuberty
  labels:
    test_id: {{ .Values.test_id }}
spec:
  source:
    file: auth-service-tests-file-{{ .Values.file_digest }}
    revision: {{ .Values.revision }}
  tests:
  - name: main
    file: test_binary
```
