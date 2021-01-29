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

Note: Here jaegerInstance.yaml is the custom resource of the Jaeger Operator Instance.

 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```copycommand
 kubectl delete -f https://operatorhub.io/install/jaeger.yaml
 ```
 
***Delete the Services "Service-a","Service-b" and "service-c" using below command:***

```copycommand
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/services.yaml
``` 

***Delete the "webhook" using below command:***

```copycommand
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/webhook.yaml
```

***Delete all the yaml files:***
 
 Example:
 
  ```copycommand
  rm -rf /home/student/jaegerInstance.yaml
  ```
  
  Similarly, you can delete rest of yaml files.
