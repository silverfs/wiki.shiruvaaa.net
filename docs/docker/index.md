# Docker in 2 minutes
![[docker.png]]


## What is Docker?

Docker is a platform for developing, shipping, and running applications in containers.
It uses a virtualization technology that makes it easier to develop and deploy apps inside of neatly packaged virtual containerized environments. Meaning, applications run the same, no matter where they are or what machine they are running on.

Docker containers can be deployed to just about any machine without any compatibility issues. your software stays system agnostic, making software simpler to use, less work to develop and easier to maintain and deploy. These containers running on your computer or server act like little micro computers, each with very specific jobs, each with their own OS, their isolated CPU processes, memory and network resources. Because of this, they can be easily added, removed, stopped and started again without affecting each other or the host machine. Containers usually run one specific task, such as a MySQL database or a NodeJS application. They can be networked together and potentially be scaled as well.

Docker is a form of virtualization, but, unlike normal virtual machines, the resources are shared directly with the host. Not only that, but Docker uses less disk space as well, as it is able to reuse files efficiently by using a layered file system.
<br>

![[container-vs-vms.png]]