## Cloud Computing Applications Part 1: Cloud Systems and Infrastructure

The first cloud computing applications course covered how the cloud is built, organised, structured to be effective. It taught the economics behind cloud computing, the technologies that enables it and the many different types of cloud services offered.

### Week 1 Cloudonomics, Big Data, Cloud Services

The arguments for using cloud computing revolve around economics and ease of use. Owning your own information systems requires a large initial investment, heavy fixed costs. You will often have more computing power than you need because you had to buy capacity that represents your highest usage even though this may only be required a fraction of the time. Often times running these systems is complex, so it can take a long time to set up and may fail at times. These factors drove the emergence of cloud computing services.

Demand for computing services is often spiky - there is a high peak demand, but much lower average demand. This usage patterns makes **utility pricing** ideal, so that you only pay for the usage of infrastructure when you need it. For the cloud provider, by sharing the infrastructure among many clients they achieve higher average utilisation of the infrastructure. By achieving higher utilisation of the infrastructure the cloud providers get a higher return on the investment on the infrastructure that they buy. At the same time, the cost for the services can be lowered. The clients then also save money in this model, as they only pay when they actually use it.

Big data is usually defined as data sets so large and complex that it becomes impossible to store and process on a single computer using traditional methods. The data sets are often to large to transmit over the internet and the usage and processing of this data requires lot of infrastructure. Big data comes from source such as mobile phones, sensors, logs, cameras, social networks and medical technology. Data collection and storage continues to grow and there is increasing demand for the services used to handle it. Graphs are one important example of the type of big data we want to be able to process and graphs that represent social networks, the web and the brain have a huge amount of vertices and edges.

Software defined architecture is the mechanism for providing services, orchestration and provisioning. There is software defined networking, storage and compute. This facilitates providing services at various levels - software, platform and infrastructure. Orchestration refers to the composing of architecture, connecting workflows and allows for scaling.

The different types of services that are offered through the cloud are Software as a Service (SaaS), Platform as a Service (PaaS) and Infrastructure as a Service (IaaS). Which service is used defines the proportion of the computing stack is managed by the cloud provider vs. the remainder that the client can fully control. As you move up the scale from IaaS to SaaS, there is greater scope for multi-tenancy, but with this comes less control and the client may notice that they are having to share hardware if they experience lower performance.

### Week 2 Virtualisation, OS Based Virtualisation, Containers, IaaS

In order to build systems that allow for multi-tenancy there must be some type of virtualisation that provides an abstraction for the layers below the level at which the service is provided. The virtualisation can help to avoid creating software that has dependencies on physical resources, the software only needs to care about the API that it has to use to interact with lower levels. The virtualisation paradigm can be used for distributed systems, such that the distributed nature of the system can be completely hidden from the client, greatly increasing easy-of-use. The difference levels of virtualisation include native (full), hardware assisted, para-virtualisation and OS level e.g. containers, jails, Chroot.

Native and full virtualisation utilises a virtual machine that simulates enough hardware to allow unmodified OSes to be run in isolation. Examples of full virtualisation software includes VirtualBox, Virtual PC and QEMU. They utilise a a hypervisor above the hardware, which handles instructions from the guest OSes that might other break their isolation, other instructions run directly on the hardware. In this way higher performance is achieved as only a subset of instructions from the OS goes through the hypervisor.

Native Virtualisation (from course slides):

![Native Virtualisation](images/native_virt.png 'Native Virtualisation')

In hardware enabled virtualisation the virtual machine has its own hardware which is enabled by specialised hardware such as Intel VT and AMD-V. Examples include VMWare Fusion and Parallels Workstation.

Hardware Enabled Virtualisation (from course slides):

![Hardware-Assisted](images/hardware_virt.png 'Hardware Enabled')

Paravirtualization uses virtual machines to simulate multiple instances of underlying hardware environment such as address spaces. It can use a modified guest OS that has a special API to communicate with the hypervisor. Examples include XEN, KVM and Win4Lin 9x. The downside of this system is that the hypervisor calls have higher overhead than direct communication with the hardware.

Paravirtualization (from course slides):

![Paravirtualization](images/para_virt.png 'Paravirtualization')

OS-level virtualisation allows for the virtualisation of physical servers at the OS-level, which enables multiple virtualised, isolated servers to run on a single physical server. Examples include Parallels Workstation, Linux-VServer and FreeBSD Jails. We can use containers or hypervisors. Containers are more elastic, but hypervisors have the advantage of supporting different OS families on a single server when using a IaaS model.

OS-level Virtualisation (from course slides):

![OS-level Virtualisation](images/os_virt.png 'OS-level Virtualisation')

Containers are very for building parallelised applications in way that can be quickly switched between. They do not offer as much isolation and security as lower levels of virtualisation. Docker provides a formalised API for software to run as a container, which makes it easy to run in the same way regardless of the the environment. It provides a layer of abstraction between applications and the specific OS and hardware. To the application it looks like it has an entire OS to itself. Once built by the docker engine, images are stored and controlled from the docker container image registry, which provides a kind of version control.

Docker (from course slides):
![Docker](images/docker.png 'Docker')

Kubernetes is a platform originally developed by Google and is for orchestrating the deployment, scaling and operations of containers across clusters of hosts. It is composed of loosely coupled building blocks. Pods give a unique IP address to a collection of containers and a persistent shared volume that lasts for the duration of the pod rather than the duration of individual containers. Labels can be attached to API objects such as pods or nodes and selectors can then be used to search for and find these objects. The Kubernetes architecture is split into master/nodes. The master contains the controller and the nodes container one or more pods of containers. Controllers are responsible for managing pods and include replication controllers (how many instances of a pod to have), daemon set controllers and job controllers. Services include sets of pods working together, label selector, service discovery and request routing and a round robin load balancer to IP address. The developer communicates with the master through its API server and users communicate with the nodes using the Kube-proxy.

The Java Virtual Machine (JVM) is an abstract computing machine that enables a computer to run a Java program. The Java Runtime Environment (JRE) is a software package that contains what is required to run a Java program and includes the Java Virtual Machine implementation and the Java Class Library. Multiple languages can be compiled into byte code including Java, Ruby, Python, Clojure, Groovy and Scala. It is relevant for distributed computing because it increases the portability of Java programs as byte code is **platform independent**.

OpenStack is a collection of open source software projects that helps to setup and run cloud compute and storage infrastructure. The architecture is composed of a dashboard, compute, network and storage components that are assigned to user applications. Once a user application is no longer needed, the components can be reallocated to other applications. These attributes are ideal for providing IaaS.

Amazon Web Services (AWS) provide many different types of cloud services such as storage, computation, elastic MapReduce, databases and coordination. The services are paid for based on usage. There are multiple regions that users can be directed to in order to get lower latency. Services can run on multiple types of instances depending on the desired optimisation e.g. general purpose, GPU instances, memory optimised. Users can choose to use their own OS or use an Amazon Machine Image (AMI). The Simple Storage Service (S3) provides object-based persistent storage and Elastic Block Storage can be attached and detached from EC2 compute instances.

Microsoft provides cloud services through Microsoft Azure. A big part of their business is Office 365, an example of SaaS. It allows for hybrid cloud architecture, where users connect their own cloud to the Microsoft cloud. Azure can be used as IaaS and PaaS too and it is particular good for support for .NET developers. It has support for Windows Server, it can provision and manage virtual machines and users can attach and manage disks. Windows Azure is the OS for the data centre and treats the data centre as a machine. It handles resource management, provisioning, monitoring and the handling of application lifecycles.

Google is another cloud service provider and has a particular focus on advertising services, which is a strong point due to its position as a leading search provider. It offers PaaS and IaaS. There is the Google App Engine which runs Python, Java, PHP and Go and uses autoscaling. The services it offers include storage, NoSQL storage, MySQL storage, Hadoop, Pub/Sub and business analytics. It offers IaaS in the form of the Google Compute Engine. The SaaS offerings include include Maps, Translate, Forms, Sites (wiki), Shopping and Keep.

Serverless architecture is where you have server-side logic written by a developer that runs on stateless compute containers in response to certain events. It can be seen as Functions as a Service (FaaS). AWS Lambda is a popular implementation of this model. Users of the service pay only for the requests that are served and the compute time used. Once the function has finished running, the user is no longer paying for any compute services. As the service is stateless, there is no storage cost beyond the lifetime of the function call. The managed nature of the service allows users to focus on the logic of the program rather than orchestration of compute infrastructure, which makes it easy to use. Within AWS Lambda, there can be many different sources of events and can include S3, DynamoDB, Kinesis Streams etc.

### Week 3 MaaS, PaaS, Web Middleware

Metal as a Service (MaaS) is the providing of server machines themselves. The users can choose which OS to install, how the drives and network are configured, what the access credentials are and when they no longer need the server. The MaaS system takes care of the management of individual units and can quickly provision and destroy machines. MaaS has region controllers which have a web UI and API for users to interact with and these region controllers communicate with cluster controllers. The MaaS dashboard gives an overview of the status and specification of individual servers and allows users to take actions such as power on and power off servers. When servers power on they use PXE boot to load their OS and configuration across the network. Juju is open source software used for managing services running on MaaS.

Google App Engine (GAE) is an example of PaaS. It supports multi tenancy and offers automatic scaling for web applications. GAE provides infrastructure tools that enable users to deploy code without worrying about infrastructure challenges. The applications running in a secure, sandboxed environment that isolates applications from the hardware and OS. Data can be stored in the App Engine Datastore, Google Cloud SQL or Google Cloud Storage. It is commonly used for developing mobile apps.

Salesforce is a large provider of SaaS and PaaS. It has focused a lot on Customer Relationship Management (CRM) services. CRM is the process by which businesses and organisations manages its interactions with customers, often relying on the collection and analysis of data. Data can enter the system through channels such as the company's website, communication mediums such as email and live chat as well as social media. The data analysis often focuses on learning more about customers in order to aid with product development, marketing and customer retention.
