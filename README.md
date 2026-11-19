\# CST8915 Lab 7 – Kubernetes Basics



\## Demo Video

\[YouTube](https://youtu.be/ts8EfmKH2C8)



\## Lab Work



I reused the AKS cluster from Lab 6 instead of creating another one, since we already had everything running in Mexico Central. I created a separate namespace called `lab7` to keep the exercises away from the Pet Store application.



I first created an NGINX Pod directly with kubectl, then created another one using a YAML file. After that, I created a Deployment with three replicas and exposed it through a LoadBalancer. All three replicas were running, and I could access the NGINX welcome page through the public IP.



\## RabbitMQ Persistence Problem



This was probably the more interesting part of the lab for me.



RabbitMQ was running with only one replica and no persistent storage configured. I wanted to see what would happen if Kubernetes had to replace its Pod, so I created a virtual host called `lab7test` and then deleted the RabbitMQ Pod.



Kubernetes immediately created another Pod, which was exactly what I expected after Lab 6. However, when I checked RabbitMQ again, the virtual host was gone. Only the default `/` remained.



Although it is expected behavior, it still feels a bit funny when you think about it. Kubernetes successfully recovered the application, but apparently forgot everything the application was supposed to remember. It is almost like replacing someone's computer after it breaks, giving them a completely new one, and saying the problem is solved even though all their files are missing.



Of course, Kubernetes is doing what we asked it to do. We never configured RabbitMQ to keep its data somewhere that would survive replacing the container.



\## Possible Solutions



The simplest solution would be to give RabbitMQ persistent storage using a PersistentVolumeClaim (PVC). This would allow its data to survive Pod replacement, assuming the volume is properly configured and reused.



A StatefulSet would also make more sense than a regular Deployment for something like RabbitMQ because it is designed to manage stateful applications. We could also use the RabbitMQ Kubernetes Operator instead of managing everything manually.



Persistent messages and durable queues would be important as well, although they would not solve the storage problem on their own.



\## Would Azure Service Bus Solve This?



Azure Service Bus is another interesting option because Azure would handle message storage and the messaging infrastructure for us.



It would solve the specific problem of depending on a RabbitMQ Pod's local storage, but it would also mean changing our application. The Order Service currently sends messages to RabbitMQ, so we could not simply replace RabbitMQ with Service Bus and expect everything to work without modifying the code.



I think it depends on what we are trying to achieve. For this lab, replacing RabbitMQ with an entirely different messaging service seems a little excessive when we could just configure persistent storage.



However, for a larger application running entirely on Azure, Service Bus might make more sense. There is less infrastructure to manage ourselves, although we would become more dependent on Azure.



\## References



\- \[RabbitMQ – Reliability Guide](https://www.rabbitmq.com/docs/reliability)

\- \[RabbitMQ – Kubernetes Deployment](https://www.rabbitmq.com/docs/next/install-kubernetes-diy)

\- \[Microsoft – Azure Service Bus Overview](https://learn.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview)

