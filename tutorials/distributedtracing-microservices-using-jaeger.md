---
title: Distributed Tracing for Microservices using Jaeger
description: Learn how to autotrace microservices using Jaeger
---

### What is Distributed Tracing?

Distributed Tracing is a technique to follow a single transaction, which often requires multiple component interactions downstream capturing timing and other metadata throughout the transaction, then reassemble information to provide a complete picture of the application’s behavior at runtime.

### Why do we need Distributed Tracing ? 
Distributed tracing enables developers to perform:

- Distributed transaction monitoring.
- Troubleshooting and root cause analysis.
- Performance and latency optimization.
- Service dependency analysis.
- Identification of the quality and reliability of software.
- Distributed context propagation.


### Basic Terminology in Distributed Tracing


**Span:** A span represents a logical unit of work in Jaeger that has an operation name, the start time of the operation and the duration. Spans may be nested and ordered to model causal relationships. 
The recommended solution is to annotate spans with tags or logs.

**Tags:**  are used as attributes/metadata in spans.

**Logs:** are like a regular log statement—they contain a timestamp and some data, but are associated with the span from which it was logged.

**Span Context:** A set of trace identifiers injected into each request, which the next service will extract and use to propagate the trace.


![](_images/span-context.png)


**Trace:** A trace is a data/execution path through the system and can be thought of as a directed acyclic graph (DAG) of spans. 

Below is a diagrammatic view of DAG. 


![](_images/distributedtracingpic3.png)


In above figure, each trace has a unique identifier (e.g. a trace id) for the trace, as span context that is propagated over the network to the next request.


**Baggage:** Key-value pairs are added to the span context and propagated throughout the trace.
An external process can inject baggage by setting the special HTTP Header jaeger-baggage on a request.

### Tools for Distributed Tracing

There are three popular open source tools that can be used for distributed tracing in microservices:

- Zipkin
- Jaeger
- AppDash

We will use here Jaeger as a tool for distributed tracing in microservices.

### Enable Tracing for an Application

Let’s take an example to understand how Jaeger facilitates tracing of microservices. A small development effort is required to hook up tracing in first place.
In this example of OpenTracing, Java special agent is used. 
Plugging this into the application via -javaagent JVM flag, enables tracing across any common 3rd party libraries without changing any code or rebuilding the project.
Here, we auto-plugin the agent at the time of deployment using a combination of webhook, and init container, and will tweak the environment variables to insert the agent.


The functionality of webhook source code will be: 
- To check the incoming deployment descriptor for the correct tags (autotrace: enabled). If present, it’ll apply Step 2 onward, otherwise it’ll leave everything untouched.
- To add a volume mount into which the opentracing special agent jar will be dropped.
- To add an init container to copy the jar into the shared mount before the application boots.
- To perform tweaking of the JAVA_TOOL_OPTIONS environment variable to add the javaagent line for the agent.


### Deploy the Application in Kubernetes Cluster

**Step 1: Verify that Jaeger is running in the `default` namespace by running the command below.** 

```execute
kubectl get all 
```

This should produce the below output.

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

**Step 2: Deploy the "webhook".**

  The `webhook.yaml` file is present inside : /home/student/projects/community-jaeger-operator-yaml
  
  - Deploy the webhook application using command below.
  
  ```execute
  kubectl create -f /home/student/projects/community-jaeger-operator-yaml/webhook.yaml
  ```
  
  You will see the output as below:
  
 ```
 service/auto-tracing-mutating-webhook created
 deployment.apps/auto-tracing-mutating-webhook created
 mutatingwebhookconfiguration.admissionregistration.k8s.io/auto-tracing-mutating-webhook created
 ```


**Step 3: Check the status of all the resources created and are in "Running" state.**

Use the following command to check the status.

```execute
kubectl get all
 ```
 
The status of all the created resources should be ‘Running’. Please wait for the STATUS to be ‘Running’. 

You will see the output as below:
 
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
  
**Step 4: Label the target namespace to activate webhook for target deployment.**
 
Execute the command given below.

```execute  
kubectl label namespace default autotrace=enabled
```

This should produce the below output.

```
namespace/default labeled
```
 
**Step 5: Now we demonstrate a request across multiple services to show how tracing works end-to-end.**
  
We have `services.yaml` file to deploy at: /home/student/projects/community-jaeger-operator-yaml
    
`services.yaml` creates a chain of services A, B and C. A calls B, B calls C and C calls upstream to the world clock API. 

Each of the services is, by default, a completely trace unaware Spring Boot application.

Each of these services is tagged with the **autotrace: enabled** label so that your webhook knows to inject  instrumentation into the application at deploy time.
   
See the example below. 

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
    
- Deploy the services.yaml using below command.
    
  ```execute
  kubectl create -f /home/student/projects/community-jaeger-operator-yaml/services.yaml
  ```
  
The resulting output displays as follows.

```
service/service-a created
service/service-b created
service/service-c created
deployment.apps/service-a created
deployment.apps/service-b created
deployment.apps/service-c created
```
  
**Step 6: Make sure that all the created resources are in "Running" state using below command.
  
  ```execute
  kubectl get all
  ```
 Note: Please wait for the pod "STATUS" to be "Running", then proceed further. 
 
 Output looks like the below
  
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

**Step 7: Execute the command below to update NodePort to 32732.**


```execute
kubectl get service/service-a --output yaml > /tmp/jaeger.yaml
sed -i "s/nodePort: .*/nodePort: 32732/g" /tmp/jaeger.yaml
kubectl patch service/service-a -p "$(cat /tmp/jaeger.yaml)"
```

Output:

```
service/jaeger-query patched
```

**Step 8: Make the following curl command.**
 
 ```execute
 curl -s http://##DNS.ip##:32732/api/json/gmt/now|jq
 ```
 
Note: It will take some time to get response from the worldclock api.If you do not see any output, please wait for some time and retry the curl command.
 
 The output should look as shown below.
 
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

The above output shows that the request is made for service-a.
Services: A calls B, B calls C, and C calls upstream to the world clock API.



**Step 9: Click on the link below to access worldclock api.**

http://##DNS.ip##:32732

![](_images/services-ui.PNG)

Step 10: Now Trace these services using Jaeger UI.

Note: Please refer tutorial "Jaeger-UI" to know how we can trace these services on Jaeger UI.
  

 




