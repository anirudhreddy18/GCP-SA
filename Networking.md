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
* Flow Logs:
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
