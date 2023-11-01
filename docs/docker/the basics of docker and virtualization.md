# The Basics of Docker and Virtualization
## Pre-Virtualization World

In the pre-virtualization days, we used big server racks. Underneath, we had the physical server. we installed the desired Operating System on it and run the application on top of the operating system. Each physical machine would run only one application. You can already guess where I'm going to...

![[pre-virtualization.png]]
<br>

There are some problems with this model. First of all, we have to purchase a physical machine. Believe me, those can be very expensive. We might end up only using a fraction of the CPU or a memory of the machine. The rest of the resources would be simply wasted. Secondly, deployment time is often slow. The process of purchasing and configuring your physical server can take ages; especially for big organizations. Thirdly, it would be painful to migrate our applications to servers from different vendors.
<br>


![[hypervisor-based.png]]
<br>

Underneath we have the physical server. There, we would install the desired Operating System. On top of the OS, a hypervisor layer is introduced which allows us to install multiple virtual machines (VM) on a physical machine. Each VM can have a different Operating system. In this way, we can run multiple Operating Systems, on a single physical machine and each Operating System can run a different application. This is the traditional model of virtualization which is being referenced as the hypervisor-based virtualization. Some of the popular hypervisor providers are VMware and VirtualBox. In the early stage, users would deploy VM's on their own physical servers, but nowadays, more and more companies have been shifted to deploy VM's in the cloud with providers such as AWS and Microsoft.

There are some huge benefits with this model. First of all, it is more cost efficient. Each physical machine is divided into multiple VM's and each one only uses it's own CPU, memory and storage resources. We pay only for the compute power storage and other resources you use with no upfront commitments which is a typical pay-as-you-go model. Secondly, it's easy to scale with VM's deployed in the cloud environment. If we wanted more instances of our application, we don't need to go through the long process of ordering and configuring new physical servers. We can simply deploy a new VM with one click of a mouse. This reduces the time to configure and deploy from weeks to minutes!

of-course, it still has limitations:

First of all, each VM still needs to have an OS installed. This means that each VM has a full OS with it's own memory management, device drivers, daemons, etc. We would have to run a whole OS on one server, simply for one application. Secondly, application portability is not guaranteed. Some progress had been achieved in getting virtual machines to run across different types of hypervisors, there is still a lot of work to be done there. VM portability is still at an early stage.

---

## Container-based Virtualization

Finally, the Container virtualization technology was dropped. Docker is an implementation of container-based virtualization technologies. Let's take a look at this diagram here.
<br>

![[container-based.png]]
<br>

Underneath, we have our server. This can either be a physical machine, or a virtual machine. Then, we install the operating system on the server. on top of the OS, we install a container engine, which allows us to run multiple guest instances. Each guest instance is called a container. Within each container we install the application and all the libraries that the application depends on.

The key to understand the difference between the hypervisor-based virtualization model and the container-based model, is the replication of the models. In the traditional model, each application is running in it's own copy of the kernel and the virtualization happens at the hardware level. In the new model, we only have one model which has supplied different binaries and runtime to the applications running in isolated containers. So, the container has shared the base runtime kernel, which is the container engine. For the new model, the virtualization happens at the operating system level. Containers shared the host's OS and is thus much more efficient and light-weighted.