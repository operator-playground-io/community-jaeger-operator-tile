---
title: Jaeger Operator Tutorial to check distributed tracing using Jaeger UI
description: This tutorial explains how to check distributed tracing of services using Jaeger UI
---

### Jaeger UI

Once our microservices are successfully configured for auto-tracing we can check the traces on Jaeger UI.

Follow below steps to check traces on Jaeger UI:

1. On the "Service" dropdown, you will see all services which are deployed on cluster and enabled for autotrace as shown in below snapshot: 

![](_images/service-autotracing-jaeger-ui.png)

2. Select "service-a" from drop down. You can select the operations you want to trace using "Operation" drop-down and other drop-down options provided by the UI and click on "Find Traces" button.

![](_images/operations.png)

3. You can see number of traces produced with the unique trace id as shown in below snapshot.

 ![](_images/see-detailed-trace-of-all-the-services.png)

4. Click on the trace id and you will see all the three services traces as shown in below snapshot:

![](_images/services-tracing.PNG)

5. Also to get more details, you can expand the traces view by clicking on small down arrow option "v" against each services. You will see something similar to below snapshot:

![](_images/services-expanded-form.png)

You can also see the logs and warning details against each services operations: "Pass" and "Get" which will provide important information while tracing your services.

![](_images/logging-warning-msg.png)


6. You can compare two traces by selecting the check box corresponding to each trace id and click on "Compare" option as shown in below snapshot:


![](_images/compare-services-traces.png)

You will see the flow how these services : service-a,service-b and service-c requests flows using "Compare" option.

![](_images/compare-services.PNG)

7. You can view Architecture DAG view using the option "System Architecture" "DAG" option. See below snapshot:

![](_images/system-architecture.PNG)


