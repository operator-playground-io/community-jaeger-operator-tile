---
title: Jaeger Operator for distributed tracing using Jaeger UI
description: Learn how to check the distributed tracing of services using Jaeger UI
---

### Jaeger UI to check the distributed tracing of services

Once our microservices are successfully configured for auto-tracing we can check the traces on Jaeger UI.

Follow the steps below to check the traces using Jaeger UI.

On the UI, you will see a "Service" dropdown, listing all the services which are deployed on cluster and enabled for autotrace. See the snapshot below. 


![](_images/service-autotracing-jaeger-ui.png)


Step 1: Select "service-a" from the drop down list.

Step 2: Select the operations you want to trace using "Operation" drop-down and other drop-down options provided within the UI.

![](_images/operations.png)

Step 3: Click on "Find Traces" button.

A number of traces with unique trace id will show up on the UI. See the figure below. 

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


