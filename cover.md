<h1 align="center">Community Jaeger Operator</h1>

![Logo](_images/logo_jaeger.png)


Jaeger, inspired by Dapper and OpenZipkin, is a distributed tracing system released as open source by Uber Technologies. It is used for monitoring and troubleshooting microservices-based distributed systems.

### Core capabilities:

Jaeger is used for monitoring and troubleshooting microservices-based distributed systems, including:

- Distributed context propagation
- Distributed transaction monitoring
- Root cause analysis
- Service dependency analysis
- Performance / latency optimization
- OpenTracing compatible data model
- Multiple storage backends: Badger, Cassandra, Elasticsearch, Memory.

### Operator features:

- Multiple modes - Supports allInOne, production and streaming modes of deployment.

- Configuration - The Operator manages configuration information when installing Jaeger instances.

- Storage - Configure storage used by Jaeger. By default, memory is used. Other options include badger, cassandra or elasticsearch. On OpenShift, the operator can delegate creation of an Elasticsearch cluster to the Elasticsearch Operator if deployed.

- Agent - can be deployed as sidecar (default) and/or daemonset.

- UI - Optionally setup ingress (Kubernetes) or secure route (OpenShift) to provide access to the Jaeger UI.



### Jaeger Operator's Architectural Flow:

Jaeger can be deployed either as all-in-one binary, where all Jaeger backend components run in a single process, or as a scalable distributed system, discussed below. There are two main deployment options:

1.Collectors are writing directly to storage.

2.Collectors are writing to Kafka as a preliminary buffer.

The following diagram illustrates the overall architecture:
Illustration of direct-to-storage architecture:

![](_images/architecture-v1.png)


Illustration of architecture with Kafka as intermediate buffer:


![](_images/architecture-v2.png)

**Jaeger client libraries:**
Jaeger client, which is part of your application and is responsible for creating tracers and spans in the application code. The Jaeger client creates trace spans in the application code which get picked up by the Jaeger agent over UDP and forwarded to the Jaeger collector using a secured gRPC connection. 

**Agent:**
The Jaeger agent is a network daemon that listens for spans sent over UDP, which it batches and sends to the collector. It is designed to be deployed to all hosts as an infrastructure component. The agent abstracts the routing and discovery of the collectors away from the client.

**Collector:**
The Jaeger collector receives traces from Jaeger agents and runs them through a processing pipeline. Currently pipeline validates traces, indexes them, performs any transformations, and finally stores them.Jaeger collector stores the data so that you can query and visualize the trace spans using the Jaeger query service. 

Jaeger’s storage is a pluggable component which currently supports Cassandra, Elasticsearch and Kafka.

**Query:**
Query is a service that retrieves traces from storage and hosts a UI to display them.

**Ingester:**
Ingester is a service that reads from Kafka topic and writes to another storage backend (Cassandra, Elasticsearch).


### Objective of tutorial

In this tutorial,we are going to cover following topics:

1. Install Jaeger Operator and verify its successful installation.
2. Create Jaeger Instance and verify status of pods and all other resources.
3. How to autotrace microservices using Jaeger
4. How to check distributed tracing of services using Jaeger UI
5. Cleanup Operator

