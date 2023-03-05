## OpenStack COA Requirements
The OpenStack Foundation has developed the Certified OpenStack Administrator exam which offers a career-path based certification for OpenStack professionals. The exam is performance-based and will test the baseline skills of an OpenStack Administrator, a person with at least 6 months of OpenStack experience who provides day-to-day operation and management of an OpenStack cloud.

Below are the specific content areas (knowledge domains) and the specific tasks on which candidates may be expected to demonstrate their knowledge.

A basic understanding of Ubuntu Linux process management is assumed.

### OpenStack APIs
You must understand how to use the available APIs to complete the COA tasks:
- Use the Dashboard UI (Horizon)
- Use the OpenStack CLI client

### Identity management - 15%
- Manage and create domains, projects, users, and roles
- Understand the differences between the member and admin roles
- Create roles for the environment
- Create and manage policy files and user access rules
- Create and manage RC files to authenticate with Keystone for command line use

### Compute - 35%
- Create and manage flavors
- Create and manage compute instances (for example, launch, shutdown, terminate)
- Generate and manage SSH keys for use when connecting to instances
- Access an instance using an SSH key
- Configure an instance with a floating IP address;use it to connect to the instance
- Create instances with security groups
- Manage Nova host consoles (VNC, NOVNC, spice)
- Manage instance snapshots
- Manage instance quotas

### Object Storage - 5%
- Use the command line client to upload and manage files to Swift containers
- Manage permissions on a container in object storage

### Block Storage - 10%
- Create and manage volumes
- Attach volumes to instances
- Create a new Block Storage volume and mount it to a Nova instance
- Manage volume quotas
- Backup and restore volumes
- Manage volume snapshots (for example, create, list, recover)

### Networking - 30%
- Manage network resources (routers, networks, subnets)
- Create external/public networks
- Create project networks
- Create project routers
- Attach routers to public and project networks
- Manage network services for a virtual environment
- Manage network quotas
- Manage network interfaces on compute instances
- Create and manage project security groups and rules
- Assign security group to instance
- Create and manage floating IP addresses
- Assign floating IP address to instance
- Detach floating IP address from instance

### Image management - 5%
- Upload a new image to an OpenStack image repository
- Manage images (for example, add, update, remove)
- Understand the difference between public versus private images
- Manage image metadata/properties
- Manage image types and backends