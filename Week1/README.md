## Setting up the Virtual Machine (Week 1)

## Description
This week we explored Open nebula and learned the procedure to set up a virtual machine through [LRZ](https://www.lrz.de/).

### OpenNebula Technology

[OpenNebula](https://opennebula.org/) provides the most simple but feature-rich and flexible solution for the comprehensive management of virtualized data centers to enable private, public and hybrid IaaS clouds. OpenNebula interoperability makes cloud an evolution by leveraging existing IT assets, protecting your investments, and avoiding vendor lock-in. It is a turnkey enterprise-ready solution that includes all the features needed to provide an on-premises (private) cloud offering, and to offer public cloud services.

![open_nebula](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/open_nebula.png)


### Setting up the Virtual Machine

#### Template Formation
All the templates for the [VMs](https://www.cloud.mwn.de/#templates-tab) are visible.
In order to make a new [template](https://www.cloud.mwn.de/#templates-tab/form), the following steps were folowed:

![template_formation](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/template_formation.png)


##### General: 
General Details of the Template like Name of the VM, Description of the VM, Memory, CPU, vCPU were specified.
![general](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/general.png)


##### Storage
One image was selected from the pre-installed Ubuntu OS Images.
![storage](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/storage.png)


##### Network
A network can be selected. Network (MWN_access_186) so that the IP is private.
![network.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/network.png)


##### OS Booting
CPU architecture(x86_64) was selected.The OS was selected to be booted from disk.
![os_booting.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/os_booting.png)


##### Input/Output
The settings for the Input/Output were kept as default.
![Input_output](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/Input_output.png)


##### Context
SSH key was generated for each machine and the public key generated was added inside the SSH Public Key text box.
![context](/uploads/3c4e46d9f710537ab79f55360f9295f4/context.png)


##### Other
The other details were kept as default.
![other](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/other.png)


After the creation of the Template, an IP is generated after a few minutes and we have the virtual Machine in the running state.
![after_template_creation](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/after_template_creation.png)


### Logging in the Virtual Machine through SSH

After the virtual machine creation, the password can be set for the machine through the VNC interface after which the machine can be logged in through our own local system terminal.

![logging_in_vm.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/logging_in_vm.png)

![setting_password.png](https://github.com/IshmeetKaur/Distributed-Data-Mining-Lab/blob/master/Week1/setting_password.png)


A similar procedure needs to be followed for node in the cluster (which would be setup in the next week)

## References
1. https://opennebula.org/about/technology/
2. https://www.lrz.de/services/compute/cloud_en/
