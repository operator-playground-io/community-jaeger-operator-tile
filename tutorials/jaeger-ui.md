---
title: Distributed tracing using Jaeger UI
description: Learn how to check the distributed tracing of services using Jaeger UI
---

### Jaeger UI to check the distributed tracing of services

Once our microservices are successfully configured for auto-tracing, we can check the traces on Jaeger UI.

Follow the steps below to check the traces using Jaeger UI.

On the UI, you will see a "Service" dropdown, listing all the services which are deployed on cluster and enabled for autotrace. See the snapshot below. 


![](_images/service-autotracing-jaeger-ui.png)


Step 1: Select "service-a" from the drop down list.

Step 2: Select the operations you want to trace using "Operation" drop-down and other drop-down options provided within the UI.

![](_images/operations.png)

Step 3: Click on "Find Traces" button.

A number of traces with unique trace id will show up on the UI. See the figure below. 

 ![](_images/see-detailed-trace-of-all-the-services.png)

Step 4: Click on the trace id and you will see all the three services traces as shown in snapshot below:
![](_images/services-tracing.PNG)

For more information, you can expand the traces’ view by clicking on small down arrow (v) visible against each service.It should look like: 

![](_images/services-expanded-form.png)

Here, you can also see the logs and warning details against each service operation, indicated as "Pass" and "Get" which will provides important alerts while tracing your services.

![](_images/logging-warning-msg.png)


Step 5: To compare any two traces, select the checkbox corresponding to each trace id and click on "Compare". 


![](_images/compare-services-traces.png)

You can also view the flow of service requests.

![](_images/compare-services.PNG)

Step 6: Click on ‘System Architecture’ to view the DAG view of architecture.

![](_images/system-architecture.PNG)


### Conclusion

This way we can check the distributed tracing of services using Jaeger UI
