---
title: Jaeger Operator cleanup Tutorial
description: This tutorial explains how to cleanup Operator
---


### Cleaning Up Operator



***Delete the operator's custom resource by kubectl delete commands :***

Example:
 
 ```copycommand
 kubectl delete -f jaegerInstance.yaml 
 ```

Note: Here jaegerInstance.yaml  is the CR of the Jaeger Operator Instance.

Similarly,delete all the custom resource.
 

***Delete the operator by kubectl delete command:***
 
 
 Example:
 
 ```copycommand
 kubectl delete -f https://operatorhub.io/install/jaeger.yaml
 ```
 

 
***Delete all the yaml files:***
 
 Example:
 
  ```copycommand
  rm -rf jaegerInstance.yaml
  ```
  
  Similarly, you can delete rest of yaml files.
