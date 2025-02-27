---
title: Tracing Go Applications
kind: documentation
aliases:
- /tracing/go/
- /tracing/languages/go
- /agent/apm/go/
further_reading:
- link: "https://github.com/DataDog/dd-trace-go/tree/v1"
  tag: "GitHub"
  text: "Source code"
- link: "https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/ddtrace"
  tag: "GoDoc"
  text: "Package page"
- link: "tracing/visualization/"
  tag: "Documentation"
  text: "Explore your services, resources and traces"
- link: "tracing/advanced/"
  tag: "Advanced Usage"
  text: "Advanced Usage"
---
## Installation and Getting Started

For configuration instructions and details about using the API, see the Datadog [API documentation][1]. For manual instrumentation, use the [integrations section](#integrations) below for Go libraries and frameworks supporting automatic instrumentation.

For a description of the terminology used in APM, see the [Getting started with APM section][2]. For details about contributing, check the official repository [README.md][3].

Consult the [migration document][4] if you need to migrate from an older version of the tracer (e.g. v<0.6.x) to the newest version.

### Installation
First [install and configure the Datadog Agent][5]. See the additional documentation for [tracing Docker applications][6] or [Kubernetes applications][7].

Next, install the Go tracer from its canonical import path:

```go
go get gopkg.in/DataDog/dd-trace-go.v1/ddtrace
```

You are now ready to import the tracer and start instrumenting your code.

## Automatic Instrumentation

Datadog has a series of pluggable packages which provide out-of-the-box support for instrumenting a series of libraries and frameworks. Find the list of supported [integrations](#integrations) below.

## Compatibility
To begin tracing your Go applications, your environment must first meet the following requirements:

* Runing the Datadog Agent `>= 5.21.1`.
* Using Go `1.9+`


### Integrations

#### Framework Compatibility

Integrate the Go tracer with the following list of web frameworks using one of the following helper packages.

| Framework        | Support Type    | GoDoc Datadog Documentation                                              |
|------------------|-----------------|--------------------------------------------------------------------------|
| [Gin][8]         | Fully Supported | [gopkg.in/DataDog/dd-trace-go.v1/contrib/gin-gonic/gin][9]               |
| [Gorilla Mux][10] | Fully Supported | [gopkg.in/DataDog/dd-trace-go.v1/contrib/gorilla/mux][11]                |
| [gRPC][12]       | Fully Supported | [gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/grpc][13]     |
| [gRPC v1.2][14]  | Fully Supported | [gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/grpc.v12][15] |

#### Library Compatibility

The Go tracer includes support for the following data stores and libraries.

| Library                 | Support Type        | Examples and Documentation                                                    |
|-------------------------|---------------------|-------------------------------------------------------------------------------|
| [AWS SDK][16]           | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/aws/aws-sdk-go/aws][17]              |
| [Elasticsearch][18]     | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/olivere/elastic][19]                 |
| [Cassandra][20]         | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/gocql/gocql][21]                     |
| [GraphQL][22]           | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/graph-gophers/graphql-go][23]        |
| [HTTP][24]              | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/net/http][25]                        |
| [HTTP router][26]       | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/julienschmidt/httprouter][27]        |
| [Redis (go-redis)][28]  | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/go-redis/redis][29]                  |
| [Redis (redigo)][30]    | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/garyburd/redigo][31]                 |
| [SQL][32]               | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/database/sql][33]                    |
| [SQLx][34]              | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/jmoiron/sqlx][35]                    |
| [MongoDB][36]           | [v0.2.0][37] only   | [gopkg.in/DataDog/dd-trace-go.v1/contrib/mongodb/mongo-go-driver/mongo][38]   |
| [BuntDB][39]            | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/tidwall/buntdb][40]                  |
| [LevelDB][41]           | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/syndtr/goleveldb/leveldb][42]        |
| [miekg/dns][43]         | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/miekg/dns][44]                       |
| [Kafka (confluent)][45] | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/confluentinc/confluent-kafka-go][46] |
| [Kafka (sarama)][47]    | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/Shopify/sarama][48]                  |
| [Google API][49]        | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/api][50]           |
| [go-restful][51]        | Fully Supported     | [gopkg.in/DataDog/dd-trace-go.v1/contrib/emicklei/go-restful][52]             |

**Note**: The [integrations documentation][53] provides a detailed overview of the supported packages and their APIs, along with usage examples.

Packages must be imported, i.e.:

```go
import "gopkg.in/DataDog/dd-trace-go.v1/contrib/<package_dir>/<package_name>"
```

## Configuration

The tracer is configured with options parameters when the `Start` function is called. An example for generating a trace using the HTTP library:

```go
package main

import (
    "net/http"
    "strings"
    "log"
    httptrace "gopkg.in/DataDog/dd-trace-go.v1/contrib/net/http"
    "gopkg.in/DataDog/dd-trace-go.v1/ddtrace/tracer"
)

func sayHello(w http.ResponseWriter, r *http.Request) {
  message := r.URL.Path
  message = strings.TrimPrefix(message, "/")
  message = "Hello " + message
  w.Write([]byte(message))
}

func main() {
    // start the tracer with zero or more options
    tracer.Start(tracer.WithServiceName("test-go"))
    defer tracer.Stop()

    mux := httptrace.NewServeMux() // init the http tracer
    mux.HandleFunc("/", sayHello) // use the tracer to handle the urls

    err := http.ListenAndServe(":9090", mux) // set listen port
    if err != nil {
        log.Fatal("ListenAndServe: ", err)
    }
}
```

For more tracer settings, see available options in the [configuration documentation][54].

## Change Agent Hostname

Configure your application level tracers to submit traces to a custom Agent hostname:

The Go Tracing Module automatically looks for and initializes with the ENV variables `DD_AGENT_HOST` and `DD_TRACE_AGENT_PORT`

```go
package main

import (
    "net"
    "os"

    "gopkg.in/DataDog/dd-trace-go.v1/ddtrace/tracer"
)

func main() {
    addr := net.JoinHostPort(
        os.Getenv("DD_AGENT_HOST"),
        os.Getenv("DD_TRACE_AGENT_PORT"),
    )
    tracer.Start(tracer.WithAgentAddr(addr))
    defer tracer.Stop()
}

```

## Further Reading

{{< partial name="whats-next/whats-next.html" >}}

[1]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/ddtrace
[2]: /tracing/visualization
[3]: https://github.com/DataDog/dd-trace-go/tree/v1#contributing
[4]: https://github.com/DataDog/dd-trace-go/tree/v1/MIGRATING.md
[5]: /tracing/setup
[6]: /tracing/setup/docker
[7]: /agent/kubernetes/daemonset_setup/#trace-collection
[8]: https://gin-gonic.com/
[9]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/gin-gonic/gin
[10]: http://www.gorillatoolkit.org/pkg/mux
[11]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/gorilla/mux
[12]: https://github.com/grpc/grpc-go
[13]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/grpc
[14]: https://github.com/grpc/grpc-go
[15]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/grpc.v12
[16]: https://aws.amazon.com/sdk-for-go
[17]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/aws/aws-sdk-go/aws
[18]: https://github.com/olivere/elastic
[19]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/olivere/elastic
[20]: https://github.com/gocql/gocql
[21]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/gocql/gocql
[22]: https://github.com/graph-gophers/graphql-go
[23]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/graph-gophers/graphql-go
[24]: https://golang.org/pkg/net/http
[25]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/net/http
[26]: https://github.com/julienschmidt/httprouter
[27]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/julienschmidt/httprouter
[28]: https://github.com/go-redis/redis
[29]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/go-redis/redis
[30]: https://github.com/garyburd/redigo
[31]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/garyburd/redigo
[32]: https://golang.org/pkg/database/sql
[33]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/database/sql
[34]: https://github.com/jmoiron/sqlx
[35]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/jmoiron/sqlx
[36]: https://github.com/mongodb/mongo-go-driver
[37]: https://github.com/mongodb/mongo-go-driver/releases/tag/v0.2.0
[38]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/mongodb/mongo-go-driver/mongo
[39]: https://github.com/tidwall/buntdb
[40]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/tidwall/buntdb
[41]: https://github.com/syndtr/goleveldb
[42]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/syndtr/goleveldb/leveldb
[43]: https://github.com/miekg/dns
[44]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/miekg/dns
[45]: https://github.com/confluentinc/confluent-kafka-go
[46]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/confluentinc/confluent-kafka-go
[47]: https://github.com/Shopify/sarama
[48]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/Shopify/sarama
[49]: https://github.com/googleapis/google-api-go-client
[50]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/google.golang.org/api
[51]: https://github.com/emicklei/go-restful
[52]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib/emicklei/go-restful
[53]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/contrib
[54]: https://godoc.org/gopkg.in/DataDog/dd-trace-go.v1/ddtrace/tracer#StartOption
