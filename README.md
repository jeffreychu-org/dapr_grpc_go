# Dapr gRPC Go

## Intoduction

This project indicate how to interact with gRPC server and client using [Dapr](https://dapr.io/).

## Technical Stack

- [Dapr](https://dapr.io/) - Helps developers build event-driven, resilient distributed applications. Whether on-premises, in the cloud, or on an edge device, Dapr helps you tackle the challenges that come with building microservices and keeps your code platform agnostic
- [gRPC](https://grpc.io/) - Modern open source high performance Remote Procedure Call (RPC) framework that can run in any environment

- [GO](https://golang.org/) - Open source programming language that makes it easy to build **simple**, **reliable**, and **efficient** software
- [BloomRPC](https://github.com/uw-labs/bloomrpc) - Provide the simplest and most efficient developer experience for exploring and querying your GRPC services

## Prerequisites

- Go 
- [Install the Dapr CLI](https://docs.dapr.io/getting-started/install-dapr-cli/)

## Get Started

### Run Locally

#### gRPC Server

Run this command in the root directory

```
go mod init $project_name
```

```
go mod tidy
```

Start the gRPC server in `greeting_server` normally by

```shell
go run main.go
```

Then you can test the gRPC server as below

![grpc_server_normal](.\image\grpc_server_normal.jpg)

Start the Dapr using Dapr Cli

```shell
dapr init
```

Create a Dapr config `config.yaml` that indicate dapr runnign as gRPC proxy

```yaml
apiVersion: dapr.io/v1alpha1
kind: Configuration
metadata:
  name: serverconfig
spec:
  tracing:
    samplingRate: "1"
    zipkin:
      endpointAddress: http://localhost:9411/api/v2/spans
  features:
    - name: proxy.grpc
      enabled: true
```

Start the gRPC server in `greeting_server` using Dapr by

```shell
dapr run --app-id server --app-protocol grpc --app-port 50051 --config config.yaml -- go run main.go
```

or in short form

```shell
dapr run -a server -P grpc -p 50051 -c config.yaml -- go run main.go
```

Then you can view the Dapr dashboard by 

```
dapr dashboard
```

and view the dashboard in http://localhost:8080 and remember the **Dapr gRPC Port**

![dapr_dashboard](.\image\dapr_dashboard.jpg)

Then you can test the gRPC server using Dapr as gRPC proxy as below

You must indicate the metadata when using Dapr gRPC server by specifying its `dapr-app-id` as the one you start the server using Dapr Cli

![grpc_server_dapr](.\image\grpc_server_dapr.jpg)

#### gRPC Client

Start the gRPC server in `greeting_client` using Dapr cli by

```shell
dapr run --app-id client --dapr-grpc-port 50007 --config config.yaml -- go run main.go
```

Please noted that you must indicate the metadata when calling the gRPC server like or involving the metdata using other programming language, plese view [here](dapr run --app-id client --dapr-grpc-port 50007 --config config.yaml -- go run main.go)

```go
ctx = metadata.AppendToOutgoingContext(ctx, "dapr-app-id", "server")
```

## Reference

[How-To: Invoke services using gRPC | Dapr Docs](https://docs.dapr.io/developing-applications/building-blocks/service-invocation/howto-invoke-services-grpc/)