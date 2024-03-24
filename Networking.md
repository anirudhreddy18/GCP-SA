# Networking GCP
* Project should be created & billing associated
  
## VPC & Subnets. 
* VPC is needed, before creating any resource. A VPC should have atleast 1 default Subnet.
* Multiple VPC's can be created in a single project.
* VPC's do not have any IP addresses assigned. Avoid Using Default VPC.
* Subnets are assigned to a region. A region can have multiple subnets. Subnets have CIDR range usually /24.
* 4 IP Addresses in each Subnet for Google.
* Network - VPC, Subnetwork - Subnet.
* Subnets have Private Google Access & flow logs.
* All subnets in a single vpc can communicate in each other, across regions as well.
  
### VPC Peering
* Communication between different VPC's are not allowed by default.
* VPC peering has to be done from both networks.

### Shared VPC
* VPC can be shared across the organization. Maintained in separate project called Host Project, maintained by network engineers.
* Tenant/Team projects are called Service Projects.
* Max Host Project -100, Max Service Project 100.
* Lets you share subnets with other projects. You can create resources on these subnets.
* Create a new Subnet inside Shared VPC, share it with a service project.

## Firewall  
* Allow default egress traffic(no risk), deny all incoming traffic
* Lower the number, higher the priority.
