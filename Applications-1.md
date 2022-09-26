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

Native Virtualisation:

![Native Virtualisation](images/native_virt.png 'Native Virtualisation')

In hardware enabled virtualisation the virtual machine has its own hardware which is enabled by specialised hardware such as Intel VT and AMD-V. Examples include VMWare Fusion and Parallels Workstation.

Hardware Enabled Virtualisation:

![Hardware-Assisted](images/hardware_virt.png 'Hardware Enabled')

Paravirtualization uses virtual machines to simulate multiple instances of underlying hardware environment such as address spaces. It can use a modified guest OS that has a special API to communicate with the hypervisor. Examples include XEN, KVM and Win4Lin 9x. The downside of this system is that the hypervisor calls have higher overhead than direct communication with the hardware.

Paravirtualization:

![Paravirtualization](images/para_virt.png 'Paravirtualization')

OS-level virtualisation allows for the virtualisation of physical servers at the OS-level, which enables multiple virtualised, isolated servers to run on a single physical server. Examples include Parallels Workstation, Linux-VServer and FreeBSD Jails. We can use containers or hypervisors. Containers are more elastic, but hypervisors have the advantage of supporting different OS families on a single server when using a IaaS model.

OS-level Virtualisation:

![OS-level Virtualisation](images/os_virt.png 'OS-level Virtualisation')

Containers are very for building parallelised applications in way that can be switched quickly between. They do not offer as much isolation and security as lower levels of virtualisation. Docker provides a formalised API for software to run as a container, which makes it easy to run in the same way regardless of the the environment. It provides a layer of abstraction between applications and the specific OS and hardware. To the application it looks like it has an entire OS to itself. Once build by the docker engine, images are stored and controlled from the docker container image registry, which provides a kind of version control.

Docker:
![Docker](images/docker.png 'Docker')
