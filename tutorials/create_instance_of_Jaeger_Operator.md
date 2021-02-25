---
title: Jaeger Operator Instance Creation
description: Learn how to create instances of your Jaeger Operator
---

### Create Jaeger Operator Instance 

Jaeger Operator Instance can be created using the Custom Resource Definition YAML files.

**Step 1: First, create the yaml definition of the Custom Resource as below.**

```execute
cat <<'EOF' >jaegerInstance.yaml 
apiVersion: jaegertracing.io/v1
kind: Jaeger
metadata:
  name: jaeger
EOF
```

**Step 2: Execute the command below to create Jaeger Operator instance in the namespace "operators"**

```execute
kubectl create -f jaegerInstance.yaml 
```

You will see a similar output as below:

```
jaeger.jaegertracing.io/jaeger created
```

**Wait till Pod STATUS is "Running", then proceed.**


**Step 3: Check the pod status.**

```execute
kubectl get pods
```

You will see a similar output as below:

```
NAME                      READY   STATUS    RESTARTS   AGE
jaeger-788f55ddc9-4m278   1/1     Running   0          8s
```

**Step 4: Check all the Kubernetes resources using the command below.**

```execute
kubectl get all
```

You will see a similar output as below:

```
NAME                          READY   STATUS    RESTARTS   AGE
pod/jaeger-788f55ddc9-4m278   1/1     Running   0          3m38s

NAME                                TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                                  AGE
service/jaeger-agent                ClusterIP   None             <none>        5775/UDP,5778/TCP,6831/UDP,6832/UDP      3m38s
service/jaeger-collector            ClusterIP   10.110.111.12    <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   3m38s
service/jaeger-collector-headless   ClusterIP   None             <none>        9411/TCP,14250/TCP,14267/TCP,14268/TCP   3m38s
service/jaeger-query                ClusterIP   10.109.168.225   <none>        16686/TCP                                3m38s
service/kubernetes                  ClusterIP   10.96.0.1        <none>        443/TCP                                  11h

NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/jaeger   1/1     1            1           3m38s

NAME                                DESIRED   CURRENT   READY   AGE
replicaset.apps/jaeger-788f55ddc9   1         1         1       3m38s
```


### Access Jaeger's dashboard

To access Jaeger services externally, first update the service: "jaeger-query" of TYPE `NodePort`.

**Step 1: Execute the command below to update the service:"jaeger-query" of TYPE NodePort.**

```execute
kubectl get service/jaeger-query --output yaml > /tmp/jaeger.yaml
sed -i "s/type: .*/type: NodePort/g" /tmp/jaeger.yaml
kubectl patch service/jaeger-query -p "$(cat /tmp/jaeger.yaml)"
```

This should produce the following output.

```output
service/jaeger-query patched
```

**Step 2: Execute the following command to set NodePort service to 32385.**

```execute
kubectl get service/jaeger-query --output yaml > /tmp/jaeger.yaml
sed -i "s/nodePort: .*/nodePort: 32385/g" /tmp/jaeger.yaml
kubectl patch svc jaeger-query -p "$(cat /tmp/jaeger.yaml)"
```

This should produce the following output.

```
service/jaeger-query patched
```

**Step 3: Click on the link below to access Jaeger Dashboard.**

http://##DNS.ip##:32385


The Jaeger Dashboard UI looks like this.


 ![](_images/jaeger-ui.PNG)


### Conclusion

Now we are able to access Jaeger Dashboard and will be able to see the details on UI.

