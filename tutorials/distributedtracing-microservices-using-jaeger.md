---
title: Enabling Distributed Tracing for Microservices With Jaeger
description: This tutorial explains how to autotrace microservices using Jaeger
---

### What is Distributed Tracing

Distributed Tracing is a technique to follow a single transaction, which often requires multiple component interactions downstream capturing timing and other metadata throughout the transaction, then reassemble information to provide a complete picture of the application’s behavior at runtime.

### Why Distributed Tracing Is Required
Distributed tracing enables developers to perform:

- Distributed transaction monitoring.
- Troubleshooting and root cause analysis.
- Performance and latency optimization.
- Service dependency analysis.
- Identification of the quality and reliability of software.
- Distributed context propagation.


### Key Terms in Distributed Tracing

Below are few terminology used while doing auto-tracing of micro-services.

**Span:** A span represents a logical unit of work in Jaeger that has an operation name, the start time of the operation and the duration. Spans may be nested and ordered to model causal relationships. 
The recommended solution is to annotate spans with tags or logs.

**Tags:**  are used as attributes/metadata in spans.

**Logs:** are like a regular log statement—they contain a timestamp and some data, but are associated with the span from which it was logged.

**Span Context:** A set of trace identifiers injected into each request, which the next service will extract and use to propagate the trace.


![](_images/span-context.png)

**Trace:** A trace is a data/execution path through the system and can be thought of as a directed acyclic graph (DAG) of spans. 

Below is a diagrammatic view of DAG. Each trace has a unique identifier (ex: a trace id) for the trace, as span context that is propagated over the network to the next request.


![](_images/distributedtracingpic3.png)

**Baggage:** Key-value pairs are added to the span context and propagated throughout the trace.
An external process can inject baggage by setting the special HTTP Header jaeger-baggage on a request.

### Tools for Distributed Tracing

There are three popular open source tools that can be used for distributed tracing in microservices:

- Zipkin
- Jaeger
- AppDash

We will use here Jaeger for distributed tracing in microservices.

### Enabling Tracing for an Application

We will take a simple example to understand how Jaeger can be used to trace microservices.
There’s some small amount of development required to get tracing hooked up in the first place.
For opentracing in this particular example of java src code,Java special agent is used. 
By plugging this into our app via the -javaagent JVM flag we can fully enable tracing across any commonly used 3rd party libraries without changing any code or rebuilding the project.
What we’ll do here is automatically plug-in this agent at deployment time using a combination of webhook, init container and tweaking of environment variables to insert the agent.

The source code of "webhook" will:
- Check the incoming deployment descriptor for the correct tags (autotrace: enabled). If present it’ll apply steps 2 onward, otherwise it’ll leave everything untouched.
- Add a volume mount into which the opentracing special agent jar will be dropped.
- Add an init container to copy the jar into the shared mount before the application boots.
- Tweak the JAVA_TOOL_OPTIONS environment variable to add the javaagent line for the agent.


### Deploy the Application in Kubernetes Cluster

1. First make sure you have Jaeger running in your default namespace using below command:

```execute
kubectl get all 
```

Output will be similar to below output:

```
NAME                          READY   STATUS    RESTARTS   AGE
pod/jaeger-788f55ddc9-4m278   1/1     Running   0          5m32s

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                  AGE
service/jaeger-agent                ClusterIP   None             <none>        5775/UDP,5778/TCP,6831/UDP,6832/UDP      5m32s
service/jaeger-collector            ClusterIP   10.110.111.12    <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   5m32s
service/jaeger-collector-headless   ClusterIP   None             <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   5m32s
service/jaeger-query                NodePort    10.109.168.225   <none>        16686:32379/TCP                          5m32s
service/kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP                                  11h

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/jaeger   1/1     1            1           5m32s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/jaeger-788f55ddc9   1         1         1       5m32s
```

2. Deploy the "webhook".

  webhook.yaml file is present inside : /home/student/projects/community-jaeger-operator-yaml
  
  Deploy the webhook application using below command:
  
  ```execute
  kubectl create -f /home/student/projects/community-jaeger-operator-yaml/webhook.yaml
  ```
  
  You will see below output:
  
 ```
 service/auto-tracing-mutating-webhook created
 deployment.apps/auto-tracing-mutating-webhook created
 mutatingwebhookconfiguration.admissionregistration.k8s.io/auto-tracing-mutating-webhook created
 ```


3. Check the status of all the resources created are in "Running" state:

```execute
kubectl get all
 ```
 
Please wait till Pod "STATUS" is "Running" and then proceed further.

A similar to this output will be shown:
 
``` 
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/auto-tracing-mutating-webhook-67bd68dd77-76zwd   1/1     Running   0          34s
pod/jaeger-788f55ddc9-4m278                          1/1     Running   0          7m27s

NAME                                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                  AGE
service/auto-tracing-mutating-webhook   LoadBalancer   10.99.128.250    <pending>     443:32745/TCP                            34s
service/jaeger-agent                    ClusterIP      None             <none>        5775/UDP,5778/TCP,6831/UDP,6832/UDP      7m27s
service/jaeger-collector                ClusterIP      10.110.111.12    <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   7m27s
service/jaeger-collector-headless       ClusterIP      None             <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   7m27s
service/jaeger-query                    NodePort       10.109.168.225   <none>        16686:32379/TCP                          7m27s
service/kubernetes                      ClusterIP      10.96.0.1        <none>        443/TCP                                  11h

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/auto-tracing-mutating-webhook   1/1     1            1           34s
deployment.apps/jaeger                          1/1     1            1           7m27s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/auto-tracing-mutating-webhook-67bd68dd77   1         1         1       34s
replicaset.apps/jaeger-788f55ddc9                          1         1         1       7m27s

```
  
4. Make sure you label the target namespace so that the webhook gets activated for any deployment within using below command:

```execute  
kubectl label namespace default autotrace=enabled
```

Output:

```
namespace/default labeled
```
 
5. We’ll need to demonstrate a request across multiple services to show end-to-end the tracing working well.
 
   We have "services.yaml" file to deploy at: /home/student/projects/community-jaeger-operator-yaml
    
   "services.yaml" creates a chain of services A, B and C. A calls B, B calls C and C calls upstream to the world clock API. 
   Each of the services is, by default, a completely trace unaware Spring Boot application.
   Each of these services is tagged with the **autotrace: enabled** label so that our webhook knows to inject instrumentation into the application at deploy time.
   
Example:
```
spec:
  replicas: 1
  selector:
    matchLabels:
      app: service-a
  template:
    metadata:
      name: service-a
      labels:
        app: service-a
        autotrace: enabled
 ```
    
 Deploy the services.yaml using below command:
    
  ```execute
  kubectl create -f /home/student/projects/community-jaeger-operator-yaml/services.yaml
  ```
  
Output:

```
service/service-a created
service/service-b created
service/service-c created
deployment.apps/service-a created
deployment.apps/service-b created
deployment.apps/service-c created
```
  
6. Check that all the created resources are in "Running" state using below command:
  
  ```execute
  kubectl get all
  ```
 Please wait till Pod "STATUS" will be "Running" and then proceed further. 
 
 
 Output similar to this will be shown:
  
``` 
NAME                                                 READY   STATUS    RESTARTS   AGE
pod/auto-tracing-mutating-webhook-67bd68dd77-76zwd   1/1     Running   0          6m56s
pod/jaeger-788f55ddc9-4m278                          1/1     Running   0          13m
pod/service-a-56dc4665b7-d2csg                       1/1     Running   0          73s
pod/service-b-66944b7dcc-brtwx                       1/1     Running   0          73s
pod/service-c-78bb44b7d5-j4s6v                       1/1     Running   0          73s

NAME                                    TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                  AGE
service/auto-tracing-mutating-webhook   LoadBalancer   10.99.128.250    <pending>     443:32745/TCP                            6m56s
service/jaeger-agent                    ClusterIP      None             <none>        5775/UDP,5778/TCP,6831/UDP,6832/UDP      13m
service/jaeger-collector                ClusterIP      10.110.111.12    <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   13m
service/jaeger-collector-headless       ClusterIP      None             <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   13m
service/jaeger-query                    NodePort       10.109.168.225   <none>        16686:32379/TCP                          13m
service/kubernetes                      ClusterIP      10.96.0.1        <none>        443/TCP                                  11h
service/service-a                       NodePort       10.111.83.19     <none>        8080:30865/TCP                           73s
service/service-b                       ClusterIP      10.99.198.55     <none>        8080/TCP                                 73s
service/service-c                       ClusterIP      10.110.123.229   <none>        8080/TCP                                 73s

NAME                                            READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/auto-tracing-mutating-webhook   1/1     1            1           6m56s
deployment.apps/jaeger                          1/1     1            1           13m
deployment.apps/service-a                       1/1     1            1           73s
deployment.apps/service-b                       1/1     1            1           73s
deployment.apps/service-c                       1/1     1            1           73s

NAME                                                       DESIRED   CURRENT   READY   AGE
replicaset.apps/auto-tracing-mutating-webhook-67bd68dd77   1         1         1       6m56s
replicaset.apps/jaeger-788f55ddc9                          1         1         1       13m
replicaset.apps/service-a-56dc4665b7                       1         1         1       73s
replicaset.apps/service-b-66944b7dcc                       1         1         1       73s
replicaset.apps/service-c-78bb44b7d5                       1         1         1       73s
```

7. Execute below command to update NodePort to 32732:


```execute
kubectl get service/service-a --output yaml > /tmp/jaeger.yaml
sed -i "s/nodePort: .*/nodePort: 32732/g" /tmp/jaeger.yaml
kubectl patch service/service-a -p "$(cat /tmp/jaeger.yaml)"
```

Output:

```
service/jaeger-query patched
```

Make the below curl command:
 
 ```execute
 curl -s http://##DNS.ip##:32732/api/json/gmt/now|jq
 ```
 
 Note: It will take some time to get response from the worldclock api.If you do not see any output, please wait for some time and retry the curl command.
 
 You will see similar to this output:
 
 ```
 {
  "$id": "1",
  "currentDateTime": "2021-01-17T09:10+00:00",
  "utcOffset": "00:00:00",
  "isDayLightSavingsTime": false,
  "dayOfTheWeek": "Sunday",
  "timeZoneName": "GMT Standard Time",
  "currentFileTime": 132553482588346850,
  "ordinalDate": "2021-17",
  "serviceResponse": null
}
```

Above output shows request is made for service-a.Services: A calls B, B calls C and C calls upstream to the world clock API.


Click on the http://##DNS.ip##:32732 to access worldclock api. 


![](_images/services-ui.PNG)

Now its time to Trace these services using Jaeger UI.

Please refer tutorial "Jaeger-UI" to check how we can trace these services on Jaeger UI.
  

 




