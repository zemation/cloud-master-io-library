## Deploying and implementing networking resources
### Creating a VPC with subnets (e.g., custom-mode VPC, shared VPC)

In Google Cloud Platform, Shared VPC and Custom VPC are two different ways to manage virtual private clouds.

A Custom VPC is a VPC that is created and managed independently by an organization. In this model, each project has its own VPC, and resources in that project can communicate with each other within the VPC. A Custom VPC provides full control over the network topology and IP address range, allowing for greater customization and security.

On the other hand, a Shared VPC allows for sharing a single VPC network across multiple projects. In this model, one project serves as the host project, and the VPC network is created and managed in that project. Other projects can then be connected to the VPC network as service projects. Service projects can use the resources in the VPC network of the host project, but cannot modify the network configuration.

The main difference between the two is the level of control and management that the organization has over the network. With a Custom VPC, the organization has complete control over the VPC network, while with a Shared VPC, the host project controls the network, and the service projects have limited control.

Here are some best practices for managing VPC networks in Google Cloud Platform:
- Plan your VPC network architecture before creating it. Consider the number of zones, subnets, and IP addresses that you may need.
- Use multiple subnets within a VPC network to isolate different tiers of your infrastructure. For example, you can use a subnet for your application tier and another for your database tier.
- Use firewall rules to control inbound and outbound traffic between your VPC network and the internet. Ensure that your firewall rules are designed to be least privilege, meaning that they only allow the minimum amount of traffic necessary.
- Use network tags to organize and manage resources within your VPC network. You can use network tags to simplify firewall rules and route traffic between instances.
- Use Cloud NAT to allow instances on your private subnet to access the internet while still maintaining security.
- Use Cloud VPN or Cloud Interconnect to create secure connections between your VPC network and your on-premises infrastructure.
- Use Private Google Access to allow instances on your private subnet to access Google Cloud services like Cloud Storage, Cloud SQL, and BigQuery without having to go through the public internet.
- Use VPC flow logs to capture network traffic metadata to monitor, troubleshoot, and analyze network activity.
- Use auto mode or custom mode subnets carefully. Auto mode subnets are automatically created when a new region or zone is added, but they cannot be deleted. Custom mode subnets give you more control over your IP address range, but you need to manually manage them.
- Follow the principle of least privilege when granting IAM roles to users and service accounts to ensure that only the necessary permissions are given for VPC network management.


#### Creating a subnet in a custom VPC
To create subnets in a custom VPC in Google Cloud Platform, you can follow these steps:

1. Open the VPC network page in the Cloud Console.
2. Click on the name of the VPC network in which you want to create the subnets.
3. Click on the "Subnets" tab and then click the "Create subnet" button.
4. Enter a name for the subnet and select the region and the IP address range for the subnet.
5. Choose the VPC network from the dropdown menu.
6. Select the region in which you want to create the subnet.
7. Select the region in which you want to create the subnet.
  - Optionally, you can enable Private Google Access for the subnet and specify a name for the secondary IP range.
8. Once you have completed these steps, you can click the "Create" button to create the subnet. Repeat these steps to create additional subnets in the custom VPC network.

#### Creating a subnet in a shared VPC
To create subnets in a shared VPC in Google Cloud Platform, follow these steps:

1. Navigate to the VPC network page in the Google Cloud Console.
2. Select the host project that owns the shared VPC network from the dropdown menu.
3. Select the "Subnets" tab and click "Add subnet".
4. Enter the name and IP address range for the new subnet.
5. Select the region for the subnet.
6. Choose the "Custom" subnet creation mode.
7. In the "Network" dropdown, select the shared VPC network that will host the subnet.
8. In the "Subnet" dropdown, select the subnet range within the shared VPC network where the new subnet will reside.
9. Click "Create" to create the new subnet in the shared VPC network.

Note that to create subnets in a shared VPC, you must have the necessary IAM permissions in both the host project and the service project. Additionally, any VMs or other resources created in the subnets will be owned by the service project, but will use resources allocated from the host project.

### Launching a Compute Engine instance with custom network configuration (e.g., internal-only IP address, Google private access, static external and private IP address, network tags)

To launch a Compute Engine instance with custom network configurations, you can follow these steps:

1. Open the Google Cloud Console and navigate to the Compute Engine section.
2. Click the "Create Instance" button to launch a new Compute Engine instance.
3. In the "Boot disk" section, select the operating system and disk size that you want to use for the instance.
4. In the "Machine type" section, select the type of machine that you want to use for the instance.
5. In the "Identity and API access" section, select the service account and access scopes that you want to use for the instance.
6. In the "Firewall" section, select the network tags that you want to apply to the instance.
7. In the "Networking" section, select "Custom" for the network configuration and choose the following options:
  - "Network": Select the custom VPC network that you want to use for the instance.
  - "Subnetwork": Select the custom subnet that you want to use for the instance.
  - "Internal IP": Choose "Create IP address" to assign an internal-only IP address to the instance.
  - "External IP": Choose "Static" to assign a static external IP address to the instance.
  - "Private Google access": Choose "On" to enable access to Google APIs and services through the private IP address.
8. Click the "Create" button to launch the instance with the custom network configurations.

Note that if you want to use network tags to control firewall rules for the instance, you need to create the corresponding firewall rules in the VPC network.

### Creating ingress and egress firewall rules for a VPC (e.g., IP subnets, network tags, service accounts)

In computer networking, ingress traffic refers to data that is incoming to a network or device, while egress traffic refers to data that is outgoing from a network or device.

Ingress traffic is typically from an external source or network and enters into the local network or device, while egress traffic is from the local network or device and goes out to an external destination or network.

For example, when you browse the internet, the data you receive from websites is ingress traffic to your device, while the data you send to websites is egress traffic from your device.


To create ingress and egress firewall rules for a VPC with IP subnets, network tags, and service accounts on Google Cloud Platform, follow these steps:
1. Open the Cloud Console and navigate to the VPC network that you want to create firewall rules for.
2. Click on the "Firewall rules" tab.
3. Click the "Create Firewall Rule" button to create a new firewall rule.
4. Enter a name for the firewall rule and choose the appropriate direction for the rule (ingress or egress).
5. Specify the source IP address range or network tag that you want to allow or deny traffic for.
6. Specify the destination IP address range or network tag that you want to allow or deny traffic for.
7. Choose the protocol and ports that you want to allow or deny traffic for.

  - Optionally, you can specify service accounts that are allowed to send or receive traffic based on their email address.

8. Choose the action that should be taken on traffic that matches this firewall rule (allow or deny).
9. Click the "Create" button to create the firewall rule.
  - Repeat these steps as needed to create additional firewall rules.

Note: For a VPC with IP subnets, you can create firewall rules that use the source and destination IP addresses to control traffic. For a VPC with network tags, you can create firewall rules that use the source and destination network tags to control traffic. Service accounts can be specified to allow or deny traffic for specific applications running in the VPC.

### Creating a VPN between a Google VPC and an external network using Cloud VPN

Cloud VPN is a networking service provided by Google Cloud Platform that enables secure connectivity between on-premises networks and Google Cloud Platform Virtual Private Cloud (VPC) networks through an encrypted Virtual Private Network (VPN) tunnel over the public internet. This service allows organizations to connect their remote networks to their VPCs on Google Cloud Platform, enabling secure and private communication between them.

Cloud VPN supports two types of VPN tunnels: 1) Dynamic VPN tunnels using route-based VPN, which automatically establish and maintain the VPN connection, and 2) Static VPN tunnels using policy-based VPN, which require the administrator to manually configure the VPN connection.

Cloud VPN can be configured to use different encryption algorithms and protocols, and offers high availability and redundancy by providing multiple tunnels between the on-premises network and the VPC network. It is a cost-effective and reliable solution for organizations that require secure and scalable connectivity between their on-premises networks and their VPCs on Google Cloud Platform.

To create a VPN between a Google VPC and an external network using Cloud VPN, you can follow these steps:
1. Prepare the network: Before you can create a VPN connection, you must ensure that your VPC network meets the requirements for Cloud VPN. This includes creating a custom VPC network if you haven't already, creating subnets, and creating firewall rules.
2. Create a Cloud VPN gateway: A Cloud VPN gateway is a virtual machine that acts as a VPN gateway on the Google Cloud Platform side of the VPN connection. You can create a Cloud VPN gateway using the Google Cloud Console or the gcloud command-line tool.
3. Configure the external VPN gateway: You must configure the external VPN gateway to connect to the Cloud VPN gateway using IPsec VPN.
4. Create a VPN tunnel: Once the Cloud VPN gateway and external VPN gateway are configured, you can create a VPN tunnel to connect them. You can create a VPN tunnel using the Google Cloud Console or the gcloud command-line tool.
5. Verify the connection: After you create the VPN tunnel, you should verify that the connection is working correctly. You can use tools like ping and traceroute to test connectivity between the two networks.

It's important to note that Cloud VPN uses IPsec VPN to establish the connection, so you must configure your external VPN gateway to use IPsec VPN as well. Additionally, you can use Cloud Router to dynamically exchange routes between your on-premises network and your VPC network to ensure that traffic is correctly routed between the two networks.

### Creating a load balancer to distribute application network traffic to an application (e.g., Global HTTP(S) load balancer, Global SSL Proxy load balancer, Global TCP Proxy load balancer, regional network load balancer, regional internal load balancer)

A Load Balancer on Google Cloud Platform (GCP) is a service that helps to distribute network traffic across multiple instances of applications, making them more reliable, scalable, and available to users. Load balancing is essential for modern web applications as it allows them to handle large volumes of traffic without experiencing performance issues or downtime.

Google Cloud Platform provides several load balancing options depending on the needs of the application, including:

Global HTTP(S) Load Balancer: distributes HTTP and HTTPS traffic across multiple regions and across instance groups, providing scalable and highly available applications.

Global SSL Proxy Load Balancer: distributes SSL/TLS traffic from the Internet to backends that are not HTTPS-enabled, improving application security.

Global TCP Proxy Load Balancer: distributes non-HTTP and non-HTTPS TCP traffic to backends, such as MySQL or Cassandra databases.

Regional Network Load Balancer: distributes traffic within a region to backends in a single region, providing lower latency and higher throughput.

Regional Internal Load Balancer: distributes internal traffic within a VPC network to backends in a single region, providing high availability and scalability for internal applications.

These load balancing options can be configured using the GCP console, command-line tools, or APIs.

To create a load balancer to distribute application network traffic to an application in Google Cloud Platform, you can follow these general steps:
1. Determine the type of load balancer needed: Global HTTP(S) load balancer, Global SSL Proxy load balancer, Global TCP Proxy load balancer, regional network load balancer, regional internal load balancer.
2. Configure backend services: A backend service is the resource that the load balancer directs traffic to, based on a set of rules you define. You can configure backend services to use Compute Engine instances, managed instance groups, or serverless backends like Cloud Functions or App Engine.
3. Configure health checks: Health checks determine if a backend instance is healthy and able to receive traffic. You can configure a health check to periodically send a request to a backend instance and check its response status.
4. Configure forwarding rules: Forwarding rules are used to route incoming traffic to the appropriate backend service.

Here are some more specific steps for creating some of the different types of load balancers:

#### Global HTTP(S) Load Balancer:
1. Create an HTTP(s) load balancer resource and configure a global forwarding rule with a static IP address.
2. Create a backend service and configure it to use one or more managed instance groups or serverless backends.
3. Create a health check for the backend service.
4. Add the backend service to the load balancer.

#### Global SSL Proxy Load Balancer:
1. Create an SSL proxy load balancer resource and configure a global forwarding rule with a static IP address.
2. Create a target proxy and bind it to a SSL certificate.
3. Create a backend service and configure it to use one or more Compute Engine instances or serverless backends.
4. Create a health check for the backend service.
5. Add the target proxy to the forwarding rule.

#### Global TCP Proxy Load Balancer:
1. Create a TCP proxy load balancer resource and configure a global forwarding rule with a static IP address.
2. Create a target proxy and bind it to a Compute Engine instance group or serverless backend.
3. Create a health check for the backend service.
4. Add the target proxy to the forwarding rule.

#### Regional Network Load Balancer:
1. Create a network load balancer resource and configure a forwarding rule in a specific region.
2. Create a backend service and configure it to use one or more Compute Engine instances or managed instance groups.
3. Create a health check for the backend service.
4. Add the backend service to the forwarding rule.

#### Regional Internal Load Balancer:
1. Create an internal network load balancer resource and configure a forwarding rule in a specific region and subnet.
2. Create a backend service and configure it to use one or more Compute Engine instances or managed instance groups.
3. Create a health check for the backend service.
4. Add the backend service to the forwarding rule.