---
title: Jaeger Operator cleanup 
description: Learn how to cleanup the Jaeger Operator
---


### Cleaning Up Operator



**Step 1: Delete the operator's Custom Resources by using `kubectl delete` commands as below.**


 ```execute
 kubectl delete -f /home/student/jaegerInstance.yaml 
 ```

**Step 2: Delete the Operator Services "Service-a","Service-b" and "Service-c" using the `kubectl delete` command as below.**

```execute
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/services.yaml
``` 

**Step 3: Delete the "webhook" service using the `kubectl delete` command as below.**

```execute
kubectl delete -f /home/student/projects/community-jaeger-operator-yaml/webhook.yaml
```

**Step 4: Delete the operator by using `kubectl delete` command as below.**
 
  
 ```execute
 kubectl delete -f https://operatorhub.io/install/jaeger.yaml
 ```
 
**Step 5: Deleting the CSV resource.**

   **Step 5.1: Find the Prometheus CSV in the namespace "Operators".**


```execute
kubectl get csv -n operators
```

Output:
```
NAME                      DISPLAY                     VERSION   REPLACES                  PHASE
jaeger-operator.v1.20.0   Community Jaeger Operator   1.20.0    jaeger-operator.v1.19.0   Succeeded
```

  **Step 5.2: Delete the CSV.**

```execute
kubectl delete csv/jaeger-operator.v1.20.0 -n operators
```
Note: The csv value may be different from above value. In the above `delete csv` command, use the csv retrieved from `kubectl get csv` command.


**Step 6: Delete all the yaml files.**
 
 
 ```execute
 rm -rf /home/student/jaegerInstance.yaml
 ```
  
  
