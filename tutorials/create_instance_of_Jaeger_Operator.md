---
title: Jaeger Operator Tutorial to create an instance of Jaeger Operator
description: This tutorial explains how to create an instance of Jaeger Operator
---

### Create Instance of Jaeger Operator

Jaeger Operator Instance can be created using the Custom Resource Definition YAML files.

**Step 1:** Create a custom resource YAML file

```execute
cat <<'EOF' >jaegerInstance.yaml 
apiVersion: jaegertracing.io/v1
kind: Jaeger
metadata:
  name: jaeger
EOF
```

**Step 2:** Create Jaeger Operator instance in the namespace "operators"

```execute
kubectl create -f jaegerInstance.yaml 
```

You will see the following output:

```
jaeger.jaegertracing.io/jaeger created
```

Check the Pods status:

```execute
kubectl get pods
```

You will see similar to this output:

```
NAME                          READY   STATUS    RESTARTS   AGE
pod/jaeger-788f55ddc9-ztglr   1/1     Running   0          5s
```

Check all the kubernetes resources:

```execute
kubectl get all
```


You will see similar to this output:

```
NAME                          READY   STATUS    RESTARTS   AGE
pod/jaeger-788f55ddc9-ztglr   1/1     Running   0          5s

NAME                                TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                                  AGE
service/jaeger-agent                ClusterIP   None            <none>        5775/UDP,5778/TCP,6831/UDP,6832/UDP      5s
service/jaeger-collector            ClusterIP   10.100.20.172   <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   5s
service/jaeger-collector-headless   ClusterIP   None            <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   5s
service/jaeger-query                ClusterIP   10.105.144.48   <none>        16686/TCP                                5s
service/kubernetes                  ClusterIP   10.96.0.1       <none>        443/TCP                                  17h

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/jaeger   1/1     1            1           5s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/jaeger-788f55ddc9   1         1         1       5s
```


### Access Jaeger's dashboard

To access Jaeger externally, lets first update service to use NodePort:

**Execute below command to use NodePort:**

```execute
kubectl get service/jaeger-query --output yaml > /tmp/jaeger.yaml
sed -i "s/type: .*/type: NodePort/g" /tmp/jaeger.yaml
kubectl patch service/jaeger-query -p "$(cat /tmp/jaeger.yaml)"
```

Output:

```output
service/jaeger-query patched
```

**Execute below command to update NodePort to 32379:**

```execute
kubectl get service/jaeger-query --output yaml > /tmp/jaeger.yaml
sed -i "s/nodePort: .*/nodePort: 32379/g" /tmp/jaeger.yaml
kubectl patch svc jaeger-query -p "$(cat /tmp/jaeger.yaml)"
```

Output:

```output
service/jaeger-query patched
```

Click on the <a href="http://##DNS.ip##:32379" target="_blank">http://##DNS.ip##:32379</a> to access Jaeger Dashboard.
You will see the Jaeger UI as below :


 ![](_images/jaeger-ui.PNG)




