# Index 📇 :card_index:

There are a lot of definitions that float around programming, most misunderstood. Let's use this index to keep track of them all! 

I will try to organize them into categories the best that I can.

## Network Programming Definitions

__Network Automation__
: A network based framework that automates a particular task by understanding, interpreting, and creating logic. Doing this eliminates manual processes and generally reduces any rates of error on large scale tasks.

__DevOps__
: Historically, networking teams were comprised of two components, engineering and operations. A DevOps model is a culture that needs to be created among the current teams. A DevOps Engineer understands both the engineering and operations for the part of the infrastructure they have created.

__Software-defined networking__
: Inter-vendor operability has been a known issue, SDN was introduced to assist this. In an SDN scenario, a packet flow is defined from a central controller that in trun interacts with multi-vendor equipment to create/define rules based upon the required packet flow. Software driven tasks run pieces of code that control specific tasks or goals(similar to methods or functions in programming). This piece of code is triggered by controller devisions or rules, which in trun adds, modifies, or deletes configs on the multi-vendor device to ensure the rule set on the controller is adhered to.

__OpenFlow__
: OpenFlow is a communication protocol that is used for communication between different vendor's equipment for the packet flow. This standard is maintained by a group called __Open Network Foundation(ONF)__

OpenFlow has two components - controllers and switches. Controllers are used to take decisions in terms of creating a path for the packet to flow across the different connected devices. Switches are dynamically configured from the controller based upon the path that a packet needs to take.
