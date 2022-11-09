# ingress-controller-conformance

The goal of this project is to act as an executable specification in the form of a test suite, implementing a standard, actively maintained ingress-controller conformance specification.

The conformance test suite will both ensure consistency across implementations, as well as simplify the work needed for other implementations to conform to the specification.

The test suite can also be viewed through human readable descriptions of what it is testing so that implementers can understand the tests without reading source code. To achieve this the project used Behavior Driven Development (BDD).
Such descriptions are located in the [features][./features] directory.

## Running

The `ingress-controller-conformance` tool embeds copies of the Kubernetes resources that are used in the conformance checks.

#### Help

```
$ ./ingress-controller-conformance --help

Usage of ./ingress-controller-conformance:
  -format string                            Set godog format to use. Valid values are pretty and cucumber (default "pretty")
  -ingress-class string                     Sets the value of the annotation kubernetes.io/ingress.class in Ingress definitions (default "conformance")
  -no-colors                                Disable colors in godog output
  -output-directory string                  Output directory for test reports (default ".")
  -stop-on-failure                          Stop when failure is found
  -tags string                              Tags for conformance test
  -wait-time-for-ingress-status duration    Maximum wait time for valid ingress status value (default 5m0s)
```

### ingress-conformance-echo

The `ingress-conformance-echo` binary is published as docker image of the same name. The purpose of this component is to handle backend-requests made through an Ingress interface and respond using data from the original request. This, in turn, allows to build assertions on the original HTTP request as it is relayed through the ingress-controller.

```
# You may chose to pass in a TEST_ID environment variable to assert which running process actually handles the downstream request.
$ TEST_ID=sample ./echoserver
Starting server, listening on port 3000
Reporting TestId 'sample'

# You can then send test HTTP requests on localhost:3000
$ curl localhost:3000
{"TestId":"sample","Path":"/","Host":"localhost:3000","Method":"GET","Proto":"HTTP/1.1","Headers":{"Accept":["*/*"],"User-Agent":["curl/7.54.0"]}}

# You can specify headers that should be returned in the echo response using the `X-Echo-Set-Header` header.
# This header can be specified multiple times if needed. Values are a comma separated list of header names and values separated by `:`.
# Header name casing is preserved.
$ curl -i localhost:3000 -H 'X-Echo-Set-Header: X-Foo: value1' -H 'X-Echo-Set-Header: x-bar: value2,x-bar: value3'
HTTP/1.1 200 OK
Content-Type: application/json
X-Content-Type-Options: nosniff
X-Foo: value1
x-bar: value2,value3
Date: Wed, 09 Nov 2022 16:14:04 GMT
Content-Length: 313

{
 "path": "/",
 "host": "localhost:3000",
 "method": "GET",
 "proto": "HTTP/1.1",
 "headers": {
  "Accept": [
   "*/*"
  ],
  "User-Agent": [
   "curl/7.68.0"
  ],
  "X-Echo-Set-Header": [
   "X-Foo: value1",
   "x-bar: value2,x-bar: value3"
  ]
 },
 "namespace": "",
 "ingress": "",
 "service": "",
 "pod": ""
}
```

---

## Building

#### Build the `ingress-controller-conformance` CLI:

```console
$ make build
```

#### Build the Docker image:

```console
$ make build-image
```
