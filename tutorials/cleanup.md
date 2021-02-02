---
title: Jaeger Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator



***Delete the operator's custom resource by kubectl delete commands :***

Example:
 
 ```copycommand
 kubectl delete -f /home/student/jaegerInstance.yaml 
 ```

***Delete the Services "Service-a","Service-b" and "service-c" using below command:***

```copycommand
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/services.yaml
``` 

***Delete the "webhook" using below command:***

```copycommand
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/webhook.yaml
```


 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```copycommand
 kubectl delete -f https://operatorhub.io/install/jaeger.yaml
 ```
 
 ***Deleting the CSV resource ***

- Find the CSV in the namespace "operators"

Example:

```execute
kubectl get csv -n operators
```

Output:
```
NAME                      DISPLAY                     VERSION   REPLACES                  PHASE
jaeger-operator.v1.20.0   Community Jaeger Operator   1.20.0    jaeger-operator.v1.19.0   Succeeded
```

Delete above CSV value using kubectl delete csv command:

```execute
kubectl delete csv/jaeger-operator.v1.20.0 -n operators
```
Note: The csv value may be different from above value.In the above delete csv command,Use the csv retrived by kubectl get csv command.  


***Delete all the yaml files:***
 
 Example:
 
 ```copycommand
 rm -rf /home/student/jaegerInstance.yaml
 ```
  
  
