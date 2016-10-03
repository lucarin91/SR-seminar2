## Security in the Microservice Architecture
#### SR - Seminar

<small><a href="http://lucar.in">Luca Rinaldi</a></small>



## Agenda
- VM vulnerability

- NoHype

- minimize the hypervisor



## Hypervisor
The purpose of a hypervisor is to present to the guest VM a view that appears as though the operating system and applications inside the VM are running directly on some given hardware. The hypervisor achieves this by emulating the underlying hardware and arbitrating access to it. Realizing this functionality requires a large and complex body of software. It also requires a significant and frequent interaction between the guest VMs and the hypervisor. This interaction is the basis of the security threat which a malicious VM can utilize to attack the hypervisor and exploit bugs in the hypervisor or supporting visualization software to attack another VM.



## Hypervisor funtionality
It emulate:

- Processor core
- Memory
- I/O devices
- Interrupts and Timers



## NoHype
NoHype system that takes the novel approach of eliminating the hypervisor attack surface altogether. We remove the need for virtual machines to constantly interact with the hypervisor during their lifetime.



## Main Design
- Pre-allocating Memory and Cores
- Using only Virtualized I/O Devices
- Short-Circuiting the System Discovery
- Avoiding Indirection


## Pre-allocating Memory and Cores
Avoiding hypervisors control on the dynamic allocation of memory and cores.


## Using only Virtualized I/O Devices
Associate at each VM a virtual I/O devices.

This is possible because in the cloud there is not need of a lot of devices.

The VM can have only a NIC card with virtualisation technology and the Storage can be used with the network.


## Short-Circuiting the System Discovery
The guest OS do not have to run the discovery of the hardware during the execution, because there is not any hypervisor to answer.

To achieve that they modify the kernel of the guest OS perform that operation only in the startup phase cache the answer.


## Avoiding Indirection
The hypervaisor give the illusion to the gust WM o is alone on the system by starting the processo ID from 0 for each VM.

By the static allocation of the cores to VM, it can access to the corret process ID avoid indirection.

In this way also the re-route of the interrupts is not needed.



## Prototype Design
![img](img/NoHype-four_stage.png)

Xen 4.0, Linux 2.6.35.4, Intel XEON W5580, NIC Intel 82576 Ethernet controller


## VM Creation
The Xen hypervisor manage the initializzation of the virtual machine:
- the affinity of the core
- the setup the NIC by the PCI pass through
- the memory allocation using VT-d


## VM Bootup
There are three main phase:

- the `hvmloader` that load the kernel and the initial RAM disk using the network.

- the modified Linux kernel (discovery devices and capability)

- Hypervisor disengagement, thoutgh a custom linux module

- iSCI drives mount and user's code execution


## VM Shutdown
The configure the controller structure of the cpu (VMCS) to exit the VM when it receive a NMI interruption, given then the possibility to the VM to handler the exit and shutdown.



## Performance
Removing the hypervisor in the overall the VM impruve performance of the $1%$



## Problems of this approach
- static allocation of the resource can create manage problem on the cloud provider (no more than 8 VM for server)

- The modified Kernel can have vulnerability or incompatibility

note:
On the software side, NoHype [21, 22] advocates space-partitioning cores, memory and devices to a VM, detaching the virtualization layer during a VM’s normal execution time. This reduces the attack surfaces for a VM as the VM is physically isolated from other VMs as well as the management VM for most of the time. Compared to NoHype, HyperCoffer assumes a stronger adversary model that further considers physical attacks, while NoHype only considers software attacks and cannot guard against sophisticated attacks such as inspecting a VM disk, bus snooping and memory freezing. Further, HyperCoffer still retains most functionalities in a commercial hypervisor like time-multiplexing resources, which are currently absent in NoHype.



## NOVA
reduce and split the hypervisor


## Conclusions



## References
<div style="font-size: 24px;">

- Szefer, J., Keller, E., Lee, R. B., \& Rexford, J. (2011, October). **Eliminating the hypervisor attack surface for a more secure cloud.** In Proceedings of the 18th ACM conference on Computer and communications security (pp. 401-412). ACM.

- Zhang, Y., Pan, W., Wang, Q., Bai, K., \& Yu, M. (2012). **HypeBIOS: Enforcing VM Isolation with Minimized and Decomposed Cloud TCB.** Virginia Commonwealth University, Technical report.

- Steinberg, U., \& Kauer, B. (2010, April). **NOVA: a microhypervisor-based secure virtualization architecture.** In Proceedings of the 5th European conference on Computer systems (pp. 209-222). ACM.
