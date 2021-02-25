<h1 align="center">Community Jaeger Operator</h1>

![Logo](_images/logo_jaeger.png)


### Overview

Jaeger, inspired by Dapper and OpenZipkin, is a distributed tracing system released as open source by Uber Technologies. It is used for monitoring and troubleshooting microservices-based distributed systems. These include: 

- Distributed context propagation
- Distributed transaction monitoring
- Root cause analysis
- Service dependency analysis
- Performance / latency optimization
- OpenTracing compatible data model
- Multiple storage backends:Badger, Cassandra, Elasticsearch, Memory.

### Features of Jaeger Operator

- Multiple modes - Supports allInOne, production and streaming modes of deployment.

- Configuration - The Operator manages configuration information when installing Jaeger instances.

- Storage - It configures the storage used by Jaeger. Memory is the default storage. Other options include Badger, Cassandra or Elasticsearch. In OpenShift environment, the operator can delegate creation of an Elasticsearch cluster to the Elasticsearch Operator if deployed.
- Agent - It can be deployed as sidecar (default) and/or daemonset.

- UI - It optionally installs Ingress (Kubernetes) or secure route (OpenShift) to provide access to the Jaeger UI.



### Jaeger Operator Architecture

Jaeger can be deployed either as all-in-one binary, where all Jaeger backend components run in a single process, or as a scalable distributed system, discussed below. There are two main deployment options:

1. The Jaeger Collectors writing directly to storage.

2. The Jaeger Collectors writing to Kafka as a preliminary buffer.

The following diagram illustrates the overall architecture:

A. Direct-to-storage architecture:

![](_images/architecture-v1.png)


B. Architecture for Kafka as intermediate buffer


![](_images/architecture-v2.png)

**Jaeger client libraries:**A Jaeger client is basically a part of your application, and is responsible for creating the tracers. It creates the trace spans in the application code which get picked up by the Jaeger agent over UDP and are forwarded to the Jaeger collector using a secured gRPC connection.

**Agent:**A Jaeger agent is a network daemon that listens to spans sent over UDP. It batches the spans and sends them to the collector. It is designed to be deployed to all hosts as an infrastructure component. The Jaeger agent abstracts the routing and discovery of the collectors away from the client.

**Collector:**A Jaeger collector receives the traces from Jaeger agents and runs them through a processing pipeline. Currently, the pipeline validates traces, indexes them, performs required transformations, and finally stores them. Jaeger collector stores the data so that you can post a query and visualize the trace spans using the Jaeger query service. 

**Storage:**Jaeger’s storage is a pluggable component which currently supports Cassandra, Elasticsearch and Kafka.

**Query:**Query is a service that retrieves traces from storage and hosts a UI to display them.

**Ingester:**Ingester is a service that reads from Kafka topic and writes to another storage backend like(Cassandra or Elasticsearch).


### Objective of tutorial

In this tutorial, we are going to cover following topics:

1. Install Jaeger Operator and verify its successful installation.
2. Create Jaeger Instance and verify status of pods and all other resources.
3. How to autotrace microservices using Jaeger.
4. How to check distributed tracing of services using Jaeger UI.
5. Clean up Operator resources.

