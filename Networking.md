# Networking GCP
* Project should be created & billing associated
  
## VPC & Subnets. 
* VPC is needed, before creating any resource. A VPC should have atleast 1 default Subnet.
* Multiple VPC's can be created in a single project.
* VPC's do not have any IP addresses assigned. Avoid Using Default VPC.
* Subnets are assigned to a region. A region can have multiple subnets. Subnets have CIDR range usually /24.
* 4 IP Addresses in each Subnet for Google.
* Network - VPC, Subnetwork - Subnet.
* All subnets in a single vpc can communicate in each other, across regions as well.
* Routes: ROutes are created per subnet, so a VPC has 2 different CIDR Ranges for 2 different subnets, 2 routes will be created. VPC router will route traffic b/w VPC. Default Internet Gateway route is also added for Internet Traffic to & fro. Routes are created for VPC Peering as well.
* Subnet ranges can be expanded from 10.0.0.0/26 to 10.0.0.0/24.
* Private Google Access: Connect to google API's using private IP Address. Enabled this setting at Subnet Level.
* Flow Logs: Enabled at Subnet Level - it will show complete packet trace. Logs wil be available in CLoud Logging. Dosent affect performance, but increases logging costs.
* Disable Public IP: Go to Networking, remove ephemeral Public IP.
  
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
* Firewall rules are created at VPC Level. They control traffic coming in & out of GCP. Rules are stateful.
* Allow default egress traffic(no risk), deny all incoming traffic
* Priority: 0 - 65535, Lower the number, higher the priority.
* Direction: Ingress or Egress
* Action: Allow or Deny
* Source: Source IP, Source tags, Service Account
* Target: All Instances, Target Tags, Service Account
* Port & Protocol: ex: tcp, 22 , http 80/443, icmp etc.
* Source can be Tag, Target can also be Tags -> ex: App tier allow traffic to DB Tier.
* Firewall rules dont work with VPC Peering. The other peered VPC should allow/deny traffic accordingly using firewall rules.

## Private Service Access
* For CloudSQL, google creates this instances in its own VPC. So Private Service Access, will be used to set Aside a CIDR range for private connections. VPC Peering will be setup to connect to CloudSQL Instance.
* Serverless VPC Access: Connect from Serverless environment like Cloud Function to Cloud SQL in different VPC. It needs a Serverless VPC Connector to route Traffic.

## IAP
* IAP provides access to manage user access to Web Apps & Cloud Resources.
* SSH & HTTP Based applications.
* Instances with Private IP cannot be SSH'ed directly. You'd need a bastion Host with a Public IP. IAP Solves this probelm by authenticating users using Google Console.
* Instances only with Private IP Can also be connected via SSH using IAP. gcloud command -- tunnel-through-iap
* Go to IAP Console -> Click on VM, add principal(User email) & Secured Tunnel User Role to User. This should allow them to SSH to VM's with Private IP.
* This role can also be added at IAM level to user, to allow them to SSH to all VM's.
* For http applications, it works similar to OIDC. You need to register app to use IAP. Then decide Internal(only Org Users) or External(all gmail accounts). Then publish your app & enable IAP. After that, you can enable IAP Secured Web User Role to Particular Users. They will be able to access Http based web application.
* Firewall Rule: By default you have to allow anywhere from the internet on Port 22. Rather than doing that, Google provides with ban IP range with 35.X.X.X. Add that as source IP in your firewall rule. This will allow only traffic from the tunnel using IAP with above IP Address.

## IP Addresses
* Public IP address will be converted by google to Private IP address to VM's using NIC's.
* Internal -> Internal IP Address. Can be reachable only with in VPC, VPC Peering, Private Service Connect, VPN. NO charge for static or ephemeral.
* External -> Ephemeral or Static. Charged even for running instances. High cost when not using public IP.
* Multiple NIC -> you can add multiple VPC's to a VM. That means now it will have 2 private IP's instead of one. VPC peering needs to be enabled if you have client that needs to connect to this VM using 2 private IP's.
* Alias & Secondary IP ranges -> Primary IP range is the primary range you assign for Subnet. But you can also attach secondary range as well. There can be multiple secondary IP ranges. When a VM is created you can assign 1 IP from Primary, 1 IP from secondary etc. Connectivity with IP's should work as there all in 1 VPC.

## GKE Networking
* Secondary IP range for subnets are assigned to Pods & Services. Assign a range to Pod, Assign a range to Services. Node pool: create default pool with 1 node for Public GKE. Assign a primary subnet in Networking section. Nodes will get Private IP from Subnet range.
* GKE Standard: pay per Cluster, GKE Autopilot: Pay per pods. Use Autopilot always. Create a shared GKE or 1 GKE per line of Business.
* Private Cluster: Create a private Cluster as above. This wont connect from Cloud Shell, since the master is in different VPC & Cloud Shell is in different VPC. Only way would be to create a Jump/Bastion Host in Same VPC & then connect to GKE Cluster. This will allow connection, as both Control Plane VPC & our VPC has been peered. Connection from Onprem VPN will not work - this is because this is peered again to our VPC, GKE wont allow third Party connections. Master node can be secured to allow only connection from Bastion Host. Control plane CIDR range is similar to Private Service Access, its provisioned in Google Controlled VPC. This VPC is peered with our VPC, so private IP communication is possible.

## Hybrid Connectivity
* Connects onPrem Network to VPC Network using VPN IPSec
* Works b/w GCP & on Prem datacenter or GCP & other Cloud Providers.
* Traffic is encrypted at one gateway & then decrypted at other gateway. Tunneled packets travel over public internet. Single Tunnel supports 3GB. You can add more than 1 tunnel per gateway.
### Static Routing  
* First Project: Create VPN gateway & attach it to our VPC. Create a Static public IP in our network & add it. Create a tunnel b/w them & enter remote peer static IP & choose route based and attach network range of onprem. Use a shared Key.
* VPN gateway will be attached at VPC. This will encrypt traffic & send it over internet using secure tunnels. The other VPN gateway will decrypt it.
* Static Routing will not work as each time new Subnet is Added, this needs to be updated on other side VPC. Doing it manually everytime is painful.
### Dynamic Routing
* Cloud Router: This will detect the new subnets created on on prem side. This is a dynamic way of routing, rather than using static way. It works with BGP Protocol & automatically adds routes. Mandatory for Dynamic Routing.
* VPN Gateway: Create a HA VPN gateway. It will create 2 Public IP's automatically for HA.
* VPN Tunnels: Create a VPN Tunnel, attach it to VPC & then select Cloud Router. After that, for peer gateway select gcp Project & other Cloud Router or Transit Gateway for AWS etc.
* BGP Session: create BGP Session, add routes automatically, choose ASN from cloud router. Then use these addresses on other project.
* When a new subnet is added - cloud router in current project will inform other router. Routes will updated accordingly.
* this will also work across regions - simply enable Cloud Router at global mode in VPC Settings.
* Network Intelligence -> connectivity Tests. Important tool to test connectivity b/w two systems.

## Cloud NAT
* when you need to reach out to Internet - you need CLoud NAT. Useful for instances or GKE with only Private IP.
* Assigned to VPC - region.
* Create NAT gateway -> assign to VPC, select all Primary & Secondary IP ranges. A cloud Router will need to be created as well.

## Cloud Interconnect
* Connecting onPrem with GCP Network. This is completely private - unlike VPN which is encrypted & travels through Internet. Communication happens using Private IP addresses(same network). Very Low latency. No encryption needed.
* Dedicated & Partner -> partner Interconnect uses connection via ISP.
