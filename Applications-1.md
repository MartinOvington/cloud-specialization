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

Web servers that are part of a cluster receive requests from the internet that have typically go through a router and then a load balancing server. The load balancing server determines which web server should serve a particular request by forwarding the request to that web server. Load balancers can be content/location aware, size aware or workload aware. The response from the web server follows the reverse route. **Middleware** is software that sits between the application layer and the OS layer and provides support to the application that is not offered by the OS. Middleware implements commonly used functionality so is reusable across different applications. The Simple Object Access Protocol (SOAP) is transmitted by HTTP or SMTP with the message coded in XML and the return value being a XML document. SOAP underlies Web Description Language (WSDL) and uses common abstractions such as procedure calls, objects and shared memory.

Remote Procedure Calls (RPCs) in distributed systems is done using request-reply communication across a network. **Remote objects** have remote interfaces that allows requests to act on it. We typically would like _exactly-once_ semantics for these calls, but this can be difficult to implement in distributed systems due to nodes failing and unreliable communication mediums. We can choose different semantics for different applications, but using _at-least-once_ semantics with idempotent operations leads to an equivalent semantic to _exactly-once_, which is often what is required. These different semantics are achieved through combinations of mechanisms such as retransmitting request messages, filtering duplicate requests, re-executing procedures and retransmitting replies.

The Hypertext Transfer Protocol (HTTP) is a communication protocol that allows the retrieving of hypertext documents on the web. Representational State Transfer (REST) relies on URI addressable resources, uses the HTTP protocol to make and receive requests using the verbs POST, GET, PUT, DELETE. These verbs act on Nouns which represent objects, data is represented mainly as JavaScript Object Notation (JSON) and XML. Servers have a REST Engine that is responsible for handling incoming requests on the objects it has.

Protocol Buffers were invented by Google for distributed services and build on the idea of RPCs. It are language-neutral and platform neutral and provides the code for both the client and the server so that users can easily set up communication between them. The Apache Thrift is another method for communication between clients and servers.

Mobile Backend as a Services (MBaaS) is the provision of commonly used services for use in mobile applications. Examples include cloud storage, user management, push notifications and integration with social media. MBaaS provide these in a one-shop model.

### Week 4 Ceph, Hive, Tez, Swift/S3, Amazon EBS Glacier, Dropbox Cloud API

Ceph is a distributed file system that is designed for performance, reliability,scalability and is more general than HDFS. Ceph consists of a Meta Data Server, Object Data Server and a Monitor. It uses reliable autonomic distributed storage for replication. The API is similar to POSIX, so it is far easier to use than HDFS. Meta data is distributed among clusters of servers, which increases performance and helps with load balancing and failure tolerance by not relying on a single central server.

Hive was started at Facebook. It is queries using Hive Query Language (HQL) a variant of SQL and is translated into map/reduce jobs, Hadoop YARN, Tez or Spark. It is similar to a SQL database and has support for relational joins. HQL is translated into an abstract syntax tree consisting of more primitive elements, which can then get translated into one of the paradigms above. Hive components includes a shell, drive, compiler, execution engine and metastore. Apache TEZ is an application framework for building of directed-acyclic-graphs (DAGs) of tasks that are used for processing data. It is used by Apache Hive and Apache Pig to run their tasks as DAGs and aims to provide performance gains relative to MapReduce by optimising the graph representing the data flow.

Swift is an object store that is accessible through the web. The storage is formatted as a Binary Large OBject (BLOB), which is a collection of binary data stored as a single entity in a databased management system. Uses cases include storing images, movies, unstructured data such as text and binary data. The motivation for BLOB storage is the huge growth in data, large amounts of unstructured data, desire for RESTful API access and desire for high availability with no single point of failure. Access URLs are typically formatted as location/version/auth_account/container/object. Clients requests are put through a load balancer, which forwards it to a proxy that then acts on object nodes. Consensus is required in the form of a quorum from all the replicas of an object. There is an MD5 checksum for each object.

Amazon S3 BLOB storage that can be accessed through web service interfaces including REST, SOAP, BitTorrent and browser GUI. It aims for high scalability, high availability and low latency. Objects are stored in buckets owned by users, who can then access the objects using keys that they have assigned to the object. Buckets are containers for objects and describe the location, the required logging, accounting info and access control. The bucket name must be globally unique and the bucket can hold any number of objects. S3 works on an **eventual consistency** model.

Amazon EBS Storage is block-level storage for instances e.g. EC2, typically temporary, but can be configured to be persistent and detachable from instances. They are located close to the instance, so provide very fast file I/O and can be either SSD or HDD. If the instance stops or is terminated or the disk drive fails, then data can be lost. Reliability can be achieved by using HDFS across multiple instances or using backups to S3 or another EBS. Different types of EBS can be requested, include general purpose SSD, provisioned IOPS SSD (guaranteed performance), throughput optimised HDD and cold HDD (inexpensive magnetic).

Amazon Glacier is for achieving data for long periods of time. The cost per GB per month is very low and the storage is extremely durable. Different storage classes are available depending on how quickly clients want requests to be able to be served, with the trade-off that quicker access solutions have higher cost. The use cases for such storage includes keeping data for compliance or regulatory purposes and to create backups for large datasets.

Amazon Elastic File System (EFS) is a network file system that is designed for enterprise customers who need a large distributed file system. It is run on SSDs, is highly available and durable and importantly it can grow and shrink as needed rather than being pre-provisioned. It achieves reliability by using redundancy across multiple availability zones. EFS is typically used for big data and analytics, media processing, content management, web serving and home directories.

Dropbox offers cloud based file storage designed to be user facing. It is easily synced across multiple devices and is accessible through a web interface, mobile apps and can be directly integrated with file system on PCs. Dropbox was initially built using Amazon S3 with the logic was run on EC2, but they have since moved to their own system. The Drop-In API allows for simple object manipulation using Choosers and Savers.The core API provides more advanced functionality like searching, revisions and allows for deeper integration with other applications. The core API has support for many languages and is based on HTTP and OAuth. The types of calls available include creating URL schemes, upload, download, viewing deltas etc.