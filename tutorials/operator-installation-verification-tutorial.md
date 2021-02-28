---
title: Jaeger Operator Installation Verification
description: Learn how to verify that the Jaeger Operator installed properly in the namespace
---

### Check the Jaeger Operator

After installation is complete, verify that Jaeger operator has been installed successfully by executing the following command

```execute
kubectl get csv -n operators
```

This should produce an output like below:

```output
NAME                      DISPLAY                     VERSION   REPLACES                  PHASE
jaeger-operator.v1.20.0   Community Jaeger Operator   1.20.0    jaeger-operator.v1.19.0   Succeeded
```

From above, we can see the following details.
a.	NAME starting with ‘Jaeger’ keyword
b.	PHASE status is Succeeded.

Do not proceed further till it shows the `PHASE` status as `Succeeded` and then proceed further.


### Check Pod Status

After the installation is successful , you can check your operator's pod by executing the below command.

```execute
kubectl get pods -n operators
```

The below output should display a pod starting with 'jaeger-operator' with Ready value '1/1' and Status 'Running' like the output as below.

```
NAME                               READY   STATUS    RESTARTS   AGE
jaeger-operator-76bdc75548-zsqt4   1/1     Running   0          60s
```

### Conclusion


Jaeger Operator is successfully installed on your cluster and has been verified using the above procedure.
