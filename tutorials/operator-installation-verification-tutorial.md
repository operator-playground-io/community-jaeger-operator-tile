---
title: Jaeger Operator Installation Verification Tutorial
description: This tutorial explains how to verify that the Jaeger Operator installed properly in the namespace
---

### Check the Jaeger Operator

After installation, verify that operator is installed successfully by executing the below command.

```execute
kubectl get csv -n operators
```

You should see a similar output as below:

```output
NAME                      DISPLAY                     VERSION   REPLACES                  PHASE
jaeger-operator.v1.20.0   Community Jaeger Operator   1.20.0    jaeger-operator.v1.19.0   Succeeded
```

**Please wait till `PHASE` status will be `Succeeded` and then proceed further.**

After the installation is successful , you can check your operator's pod by executing the below command.

```execute
kubectl get pods -n operators
```

You should see a pod starting with 'jaeger-operator' with Ready value '1/1' and Status 'Running' like the output as below.

```output
NAME                                         READY   STATUS    RESTARTS   AGE
jaeger-operator-76bdc75548-n8r6n             1/1     Running   0          11m
```

