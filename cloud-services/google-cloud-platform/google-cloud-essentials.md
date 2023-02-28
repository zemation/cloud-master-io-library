# Google Cloud Fundamentals
## Service Locations
- Locations: ie San Franscico
- Region: us-west1
- Zone: us-west1-b

### Google Infrastructure Security
The security infrastructure can be explained in progressive layers, starting from the physical security of our data centers, continuing on to how the hardware and software that underlie the infrastructure are secured, and finally, describing the technical constraints and processes in place to support operational security

We begin with the Hardware infrastructure layer which comprises three key security features:
- The first is hardware design and provenance. Both the server boards and the networking equipment in Google data centers are custom-designed by Google. Google also designs custom chips, including a hardware security chip that's currently being deployed on both servers and peripherals.
- The next feature is a secure boot stack. Google server machines use a variety of technologies to ensure that they are booting the correct software stack, such as cryptographic signatures over the BIOS, bootloader, kernel, and base operating system image.
- This layer's final feature is premises security. Google designs and builds its own data centers, which incorporate multiple layers of physical security protections. Access to these data centers is limited to only a very small number of Google employees. Google additionally hosts some servers in third-party data centers, where we ensure that there are Google-controlled physical security measures on top of the security layers provided by the data center operator

Next is the Service deployment layer, where the key feature is encryption of inter-service communication.  Google’s infrastructure provides cryptographic privacy and integrity for remote procedure call (“RPC”) data on the network. Google’s services communicate with each other using RPC calls. The infrastructure automatically encrypts all infrastructure RPC traffic that goes between data centers. Google has started to deploy hardware cryptographic accelerators that will allow it to extend this default encryption to all infrastructure RPC traffic inside Google data centers

Then we have the User identity layer. Google’s central identity service, which usually manifests to end users as the Google login page, goes beyond asking for a simple username and password. The service also intelligently challenges users for additional information based on risk factors such as whether they have logged in from the same device or a similar location in the past. Users can also employ secondary factors when signing in, including devices based on the Universal 2nd Factor (U2F) open standard

On the Storage services layer we find the encryption at rest security feature. Most applications at Google access physical storage (in other words, “file storage”) indirectly via storage services, and encryption using centrally managed keys is applied at the layer of these storage services. Google also enables hardware encryption support in hard drives and SSDs

The next layer up is the Internet communication layer, and this comprises two key security features.
- Google services that are being made available on the internet, register themselves with an infrastructure service called the Google Front End, which 
ensures that all TLS connections are ended using a public-private key pair and an X.509 certificate from a Certified Authority (CA), as well as following best 
practices such as supporting perfect forward secrecy. The GFE additionally applies protections against Denial of Service attacks.
- Also provided is Denial of Service (“DoS”) protection. The sheer scale of its infrastructure enables Google to simply absorb many DoS attacks. Google also has multi-tier, multi-layer DoS protections that further reduce the risk of any DoS impact on a service running behind a GFE

The final layer is Google's Operational security layer which provides four key features.
- First is intrusion detection. Rules and machine intelligence give Google’s operational security teams warnings of possible incidents. Google conducts Red Team exercises to measure and improve the effectiveness of its detection and response mechanisms.
- Next is reducing insider risk. Google aggressively limits and actively monitors 
the activities of employees who have been granted administrative access to the infrastructure.
- Then there’s employee U2F use. To guard against phishing attacks against Google employees, employee accounts require use of U2F-compatible Security Keys.
- Finally, there are stringent software development practices. Google employs central source control and requires two-party review of new code. Google also provides its developers libraries that prevent them from introducing certain classes of security bugs. Additionally, Google runs a Vulnerability Rewards 
Program where we pay anyone who is able to discover and inform us of bugs in our infrastructure or applications




# Google Compute Fundamentals

### Compute Engine
- Is used as server resources instead of acquiring and managing server hardware.

A machine type is a set of virtualized hardware resources available to a virtual machine instance, including the system memory size, virtual CPU count, and persistent disk limits

#### Types of Machine Types 
- E: General purpose used for day to day computing
- N: General purpose balanced price and performance
- M: Memory optimized
- C: Compute optimized

### Resource Monitoring
Monitoring resources in the cloud 
Can monitor resources in aws as well.  
Dashboards for 
- inventory
- overview
- cpu
- memory
- disk
- network
- explore

Uptime checks
- resource types 


### App Engine Fundamentals
- PaaS: Platform as a Service
- No server setup or provisioning with minimal management required
- Automatic scaling and load-balancing
- Great for websites, mobile apps, and line of business apps
- Standard Environment
  - Earliest Implementation
  - More proprietary
  - Limited languages and access
  - Faster instance spin-up
  - Less expensive

- Flexible Environment
  - Recently introduced
  - Standardized on Docker
  - Broader language/version use
  - Slower instance spin-up
  - More expensive

### Compute Engine Fundamentals
- IaaS: Infrastructure as a Service
- Scalable, high-performance virtual machines(VMs)
- Numerous configurations, completely customizable
- Run public disk images or private images
- Various storage options
- Optionally, works with containers
- Virtual Private Network (VPC) support
- Default and custom firewalls
- Complete routing support
- Health checks can be set to monitor a system based on specified parameters

### Kubernetes Engine Fundamentals
- Managed, orchestrated environment for containerized applications
- Uses Compute Engine instances to form cluster
- Relies on open source Kubernetes cluster management system
- Currently only Docker containers are supported
- Benefits:
  - Load-balancing integrated
  - Node pools supported
  - Automatic cluster and node scaling
  - Automatic upgrades
  - Automatic repair based on health reports
  - Automatic logging and monitoring with Stackdriver

### Cloud Functions Fundamentals
- Serverless environment for executing code and connecting cloud services
- Fully managed: zero infrastructure or management requirements
- JavaScript functions in a Node.js wrapper
- Triggers:
  - HTTP request
  - Cloud Storage event
  - Pub/Sub event
- Use Cases:
  - Webhooks - Respond to any HTTP request
  - Data & Image Processing - Validate/transform data or manipulate images.
  - Mobile Back end - React to storage, authentication or data events
  - Internet of Things - Respond to Pub/Sub messages from devices


# Google Identity and Security Fundamentals
### Security in Google Cloud
Data Storage Security 
- Encryption in Transit and Encryption at Rest
IAM is good for least privilege

- Granular control for access to google cloud resources
- Organizations - The organization resource represents an organization and is the root node in the Google Cloud resource Heirarchy
- Folder: Can serve as different depts or teams within the company
- Project: Serves as the hub and basis for all your cloud resources
  - A Project organizes all your Google Cloud resources
  - A project consists of a set of users; a set of APIs; and billing, authentication, and monitoring settings for those APIs

### Cloud Identity Fundamentals
- Identity as a Service: IDaaS
- Originally G-Suite based with separate Google Admin
- Integrated with Google Cloud
- Manage resources hierarchically
- Assign unmanaged accounts to projects
- Allow single sign-on

### Cloud IAM Fundamentals
- Unified resource access management system
- For both users and services
- 3 main components: policies, roles, resources
- Policy: Who can do what on which resources
- Roles: List of permissions assigned to identities/members
- Identities:
  - Google account (managed account), unmanaged account
  - Service account
  - Google Group, G-Suite Domain, Cloud Identity Domain
- Resources
  - Projects and folders
  - Cloud services (Compute Engine, Cloud Storage, Pub/Sub, etc.)
  - Aspects of those services (instances, buckets, topics, etc.)

### Cloud KMS Fundamentals
- Cryptographic key management systems
- Keys are used to encrypt/decrypt files
- Hierarchical, like IAM
  - Project > Location > Key Ring > Key > Key Version
- Cloud KMS is a project-based resource
- Specify locations:
  - Regional, Multi-Regional, or Global
- Key ring is a collection of keys in a specified location for a specific project
- Individual keys inherit permissions from key ring
- Different key versions have different encryption/decryption values
- Key version states: Enabled, Disabled, Scheduled for Destruction, Destroyed
- Key versions can be rotated regularly and automatically or manually

### Cloud IAP Fundamentals
- Application-level authorization service
- Based on internal Google enterprise security model, BeyondCorp
- Supplements network-level firewalls
- Ideal for line of business apps
- No VPN needed:
  - Straight-forward implementation for administrators
  - Simple to use for remote workers
- No additional charge

### [Learn More](https://cloud.google.com/security/security-design)

# Google Storage & Databases Fundamentals
#### Standard Storage
- Storage for data that is frequently accessed ("hot" data) and/or stored for only brief periods of time.
- Best for - "Hot" data, including websites, streaming videos, and mobile apps.

#### Nearline Storage
- Low cost, highly durable storage service for storing infrequently accessed data.
- Best for - Data that can be stored for 30 days.

#### Coldline Storage
- A very low cost, highly durable storage service for storing infrequently accessed data.
- Data that can be stored for 90 days

#### Archive Storage
- The lowest cost, highly durable storage service for data archiving, online backup, and disaster recovery.

- Best for - Data that can be stored for 365 days.

### Cloud Database Services

### Relational Databases
#### Cloud SQL
- SQL instance for MySQL, PostGres and SQL Server Engine
#### CloudSpanner
- Globally distributed database offering with unlimited scalinig

### Non Relational Databases
#### Big Table
- Large Analytical and Operational workloads
#### Firestore 
- Fully managed document database for developing applications 
#### MemoryStore
- Highly available in memory service for MemCached or Reddis

### Big Query
- Serverless, highly scalable, and cost-effective multi-cloud data warehouse designed for business agility.

### Google DataFlow
- Fully managed service for exectuing Apache Beam pipelines within the Google Cloud Platform

### Cloud Pub/Sub
- Fully managed real-time messaging service that allows you to send and receive messages between independent applications.

1. Publisher creates a topic and sends a message to the topic
2. Subscribers receive messages sent to the topic.  

### Cloud Storage Fundamentals
- Binary large objects (BLOB) storage
- Images, videos, audio files, documents, static websites, etc.
- Automatic data encryption at rest and decryption on delivery
- Primary container: buckets
  - Project-based
  - Globally unique ID
  - Specific location
  - Set class for optimum price/performance
    - Multi-regional -highest availability, most frequently accessed
    - Regional - routinely accessed, best for analytics
    - Nearline - infrequently accessed, used for archival and data backup
    - Coldline - least accessed, lowest cost, typically for disaster recovery

### Cloud Datastore Fundamentals
- NoSQL document database for semi-structured data
- Key features:
  - ACID transactions
  - Highly available and scalable
  - Multiple access options, i.e., Console, JSON, API, and open source clients
  - SQL-like language, GQL
- Structure - similar to traditional, but more flexible (schemaless):
  - Kinds - like tables
  - Entity - like row, but can have different properties
  - Property - like field, but can have multiple values
  - Key - like primary index
  - Supports optional ancestors and children
- Uses: product catalogs, user profiles, ACID transactions, etc.

### Cloud SQL Fundamentals
- Fully managed relational database service
- Supports PostgreSQL 9.6
- Supports MySQL 5.5(1st generation) and 5.6 or 5.7 (2nd generation)
- Robust scalability
- Automatic replication and backup
- Highly configurable SQL instances
- Data automatically encrypted
- Default firewalls for each instnace
- Full integration with Google Cloud services

### Cloud Bigtable Fundamentals
- Fully managed, massively scalable NoSQL database service for big data
- Used for Gmail, Google Search, Maps & Analyltics as well as eBay and Spotify
- Although also NoSQL, different from Cloud Datastore
  - Wide column database vs document database
  - No SQL-like language available
  - Single key per row
- Capable of holding hundreds of petabytes of information
- Columns wide enough for entire web pages or satellite imagery
- Consistent low latency and high throughput
- Dynamically change cluster size without stopping and restarting
- Use cases: graph data, financial data, IoT data, marketing data, etc.

### Cloud Spanner Fundamentals
- Fully managed, enterprise-grade, relational database service
- Is to CloudSQL as Cloud Bigtable is to Cloud Datastore
- Scales horizontally like NoSQL databases
- High availability (99.999% uptime) with strong consistency
- Industry standard SQL support
- Supports data definition language (DDL)
- Client libraries: C#, Go, Java, Node.js, PHP, Python, and Ruby
- Full console support
- Use cases: call centers, financial trading, telecom, transportation, etc

### Cloud MemoryStore Fundamentals
- Fully managed, in-memory datastore service
- Recently released
- Redis protocol compatible
- Extremely low latency: sub-millisecond
- As-needed scaling, up to 300GB isntance
- Connect with App Engine, Compute Engine or Kubernetes Engine
- Service tiers:
  - Basic - default, for basic caching
  - Standard - for highly available Redis instance
- Use cases: caching layer in gaming & analytical pipelines, stream processing


# Big Data Fundamentals

### BigQuery Fundamentals
- Fully managed data warehouse for big data
- Near real-time interactive analysis of massive datasets
- Analyze terabytes of data in seconds
- Standard SQL supported
- Query public or commercial dataset with your own
- External services queries: Cloud Storage, Cloud Bigtable & Google Drive
- Automatic data replication
- Modify data with Data Definition Language
- Use cases: real-time inventory, predictive digital marketing, analytical events

### Cloud Dataflow Fundamentals
- Fully managed service for creating pipelines to process data
- Based on Apache Beam
- Processes data on multiple machines in parallel
- Handles both streaming(live) and batch(archived) data
- No instances or clusters to establish: serverless
- Easy replication of services with templates
  - No need to recompile code before processing pipeline
  - Execute pipeline without dev environment and its dependencies
  - Can customize execution with template parameters
  - Can be exectued via the console or gcloud command
- Best option if no current implementation with Apache Hadoop or Spark
- Use cases: analytical dashboards, forecasting sales trends, ETL operations

### Cloud Dataproc Fundamentals
- Fully managed cluster data processing service
- Compatible with Apache Hadoop, Spark, and Hive
- Move existing projects or pipelines without redevelopment
- Boasts fast cluster creation - 90 seconds vs 5-30 minutes
- Can scale clusters up and down without stopping the job
- Can switch to different versions of Hadoop, Spark, and others
- Workflow templates recently added
  - Create templates, add job, and instantiate template
    - Workflow 1: Creates cluster, runs jobs, and deletes cluster
    - Workflow 2: Works with existing cluster and runs jobs
- Similar to Dataflow
  - Both data process
  - Both handle batch and streaming data

### Cloud Pub/Sub Fundamentals
- Fully managed messaging middleware service
- Allows secure and highly available messages between independent aps
- Works with both Google Cloud and external services
- Full range of communication:
  - One to Many
  - Many to one
  - Many to many
- Both push and pull options
- Messages encrypted and HIPAA compliant
- Use cases: streaming data, event notifications, asynchronous workflows, etc.

### Cloud Datalab Fundamentals
- Interactive data analysis and machine learning environment
- Packaged as a container and runs in a VM instance
- Based on Jupyter notebooks
- Notebooks
  - Contain code, docs in markdown, and code results
  - Code results can be text, image, JavaScript, HTML
  - Can be shared with team members
  - Collection of cells containing code or markdown

### Cloud Data Studio Fundamentals
- Interactive report and big data visualizer
- Creates dashboards, charts, and tables
- Connects to Cloud BigQuery, Cloud Spanner, Cloud SQL & CloudStorage
- Stores shareable files on Google Drive
- Basic process in three steps:
  - Connect to data source
  - Visualize data in report
  - Share report


# Networking Fundamentals
- VPC: A virtual private cloud (VPC) is a virtual version of a physical network implemented inside of Google's production network

### Cloud VPC Fundamentals
- Private network within Google Cloud network infrastructure
- Adds network to Compute Engine, Kubernetes Engine, and App Engine(Flex)
- Global resource consisting of regional subnets, connected by WAN
- Cloud VPC includes:
  - Firewalls
  - Routes
  - Forwarding rules
  - Configuration of IP addresses, external and internal
- Two types of VPC creation: Auto mode and Custom mode
- Additional services include Shared VPC and Network Peering

### Cloud Load Balancing Fundamentals
- Fully managed incoming traffic service
- Distributes traffic across several VM instances
- Benefits:
  - Autoscaling(by policy, CPU utilization, or serving capacity)
  - Supports heavy traffic
  - Route traffic to closest instances
  - Detect and remove unhealthy instances
- Types of load balancing supported
  - Global external - HTTP/S, SSL, and TCP
  - Regional external - TCP/UDP within a region
  - Regional internal - between groups of instances in a region

### Cloud CDN Fundamentals
- Accelerates delivery from Compute Engine and Cloud Storage
- Lowers network latency, offloads origin servers, and reduces serving costs
- Features include:
  - Offers SSL at no additional cost
  - Supports HTTP/1.0, HTTP/1.1 and HTTP/2
  - Supports cache invalidation
  - Cache-to-cache filling supported
- General availability caches to 10 GB, Large Object Caching(Beta) to 5 TB
- Caching considerations
  - Caching is reactive
  - Caches cannot be pre-loaded
  - Once enabled, caching is automatic
  - HTTP(S) laod balancer is required

### Cloud VPN Fundamentals
- Provides secure connection between on-premises and Cloud VPC
- Utilizes IPsec VPN gateways with encrypted/decrypted traffic
- Features include:
  - Site-to-site VPN via simple topology or with redundancy
  - Supports Internet key Exchange (IKE) v1 and v2 with shared secret
  - Uses ESP in Tunnel mode with authentication for encryption
- Routing methods supported:
  - Dynamic gateways using Border Gateway Protocol
  - Policy-based routing
  - Route-based VPN
- Best for low/medium traffic: 3 Gbps with direct peering; 1.5Gbps without

### Cloud Interconnect
- Provides higher-capacity connections between on-prem and Cloud VPC
- Dedicated Interconnect
  - Direct physical connections with Google network
  - 69 colocations facilities in 17 regions
  - Highest bandwidth: 10Gbps circut (8 circuits max)
  - Routing equipment in colocation facility required
- Partner interconnect
  - Connect to 3rd party service provider
  - Many more connection possibilities
  - Bandwidth from 50 Mbps to 10 Gbps
  - Routing equipment not required
- Public internet bypassed
- VPN tunnels or NAT devices not needed
- Not encrypted - use app level encryption or own VPN

# Cutting Edge Fundamentals
### Machine Learning
The goal of machine learning is to build computer systems that can adapt and learn from their experiences
TensorFlow is an open source library 
for machine learning at the heart of 
a strong open source ecosystem

Machine Learning Activity

Ingest Data 
- Transfer Service
- Cloud Storage

Prepare 
- Cloud Dataprep
- Cloud Dataflow
- Cloud Dataproc
- Big Query

Preprocess 
- Cloud Dataflow
- Cloud Dataproc
- Big Query

Discover 
- AI Hub

Develop 
- Data labeling service
- Deep Learning VM Image
- AI Platform Notebooks

Train 
- AI Platform Training
- Kubeflow (on-premises)

Test & Analyze 
- TFX Tools

Deploy 
- AI Platform Prediction
- Kubeflow (on-premises)
### Cloud AI Fundamentals
- Collection of services and APIs designed to facilitate machine learning
- Includes hardware accelerators: TPUs(TensorFlow Processing Units)
- Primary service: Cloud Machine Learning engine(ML Engine)
  - Training 
    - Trains computer models to recognize patterns in data
    - Supports TensorFlow, scikit-learn, and XGBoost
  - Prediction
    - Online
      - Real-time processing with fully managed ML Engines
      - No Docker container required & supports multiple frameworks
    - Batch
      - For asynchronous operations
      - Scales to terabytes of data
- Data must be stored in accessible location, e.g. Cloud Storage

### Cloud IoT Core Fundamentals
- Fully managed service for connecting and managing IoT devices
- Devices must be registered
- Works with both telemetry(event data) & device state data
- Receives data and sends to Cloud Pub/Sub topic
- Supports HTTP and MQTT protocols for communication
- Highly secure:
  - Each device uses JSON Web Tokens for public/private keys
  - Supports RSA or Elliptic Curve algorithms to verify signatures
  - Key rotation support
  - Access to IoT core controlled Cloud IAM roles and permissions
- Part of an IoT eco-system with Android Things and Google Beacon

### Cloud Data Transfer Fundamentals
- Range of options available for transferring data to Google Cloud
- Online Transfer
  - Tools available: console upload, JSON REST API, gsutil
- Storage Transfer Service
  - Imports online data to Cloud Storage
  - Supports transfer of objects from AWS S3
- Transfer Appliance
  - Physical device loaded on-prem and shipped to Google data center
  - Single device can hold petabyte of data
  - Far faster than online transfer for large amounts of data

# Google Cloud Platform Cost Control
### Basic Billing Concepts
- Pay-as-you-go
- No upfront cost
- No montly fee
- No termination fee
- Usage billed per second

### Billing resource organization

Cloud Resource heirarchy
- Establishes relationship between parent and child resources

Organizations
 - Folders
   - Projects
     - Resources

## Billing Account Perspective

### Under Organization Layer
- Policies applied to the organization are applied to the billing account
- The organization can have multiple billing accounts

### Relationship to Projects
- All projects must be associated toa billing account to create billable resources
- A billing account can include multiple projects ('one to many').

### Account Management
- Billing account access control applies to all included projects
- Costs management also applies to all included projects

## Managing Billing Access
### Control Billing Access with IAM Roles
### Why is Access Management important for controllilng costs?
- Limit account creation
  - Who can create, edit, and delete billing accounts in the organization
- Limit who can associate billing accounts with projects
  - Who can create billable resources?


Who | Can do what | On which resource 

Member | Role | Resource 


## Billing Perspective: Control Access
### Account Ownership
#### Billing Account Administrator
 - 'Owns' the billing account
 - Manages and assigns access to others

### Account Creation
#### Billing Account Creator
- Creates new billing accounts in the organization
- Cannot assign rights to others

### Associate Accounts with Projects
#### Billing Account User
- Can associate projects with billing accounts
- Prevents unrestricted creation of billable resources
- Often paired with the Project Creator

## Iam Access Scopes
#### Organization Layer
- Roles assigned at organization layer are inherited by all attached billing accounts
#### Individual Billing Account
- Roles assigned to billing account apply only to that billing account

## Best Practices
### Limit scope of access
#### Principle of least privilege
- Assign only the necessary permissions to perform the needed role
### Assign multiple billing account administrators
- If one administrator is not available, another can manage the account


## Cost Visibility with Billing Reports
### Why is this important
#### visbility into all costs
- We cannot reduce costs if we cdon't know what we're paying for to begin with
- You don't know what you don't know

### Billing Reports Answers
#### How much are we spending
#### What are our cost trends?
- Predicted costs based on current resource usage 
#### What are driving our costs?
- Breakdown costs by product, project, SKU, and others

## Export Billing Data to BigQuery
#### IMPORTANT NOTE REGARDING CHANGING EXPORT METHODS
- Billing export currently supports BigQuery and Cloud Storage export
- However, Cloud Storage export is deprecated in favor of BigQuery export 

### Why export billing data to BigQuery
#### Perform further in-depth analytics of billing data
- Pair BigQuery with data analytics tools (e.g. Data Studio)

### How to Export Billing Data in BigQuery
#### Create a BigQuery dataset
- Requires a BigQuery User role in the export project
#### Configure billing exxport to the above dataset
- Requires a Billing Account Administrator role

## Billing Alerts with Budgets
### Why configure budgets and budget alerts
#### Avoid suprises on billing
- Be alerted to runaway costs before they get out of hand

### How Budgets and Alerts Work
#### Create a budget
- Set the scope to the entire account or limit it by project ID, product, or label
#### Set alert thresholds
- Configure alerts for reaching different percentages of the monthly budget
#### Configure notifications based on threshold reached
- By default, all billing admins are notified
- Also by default, no resources are disabled after meeting budget limit
- We can optionally send notifications to Cloud Monitoring and Pub/Sub for further actions 

## Reducing Costs on GCP
### Cost Reduction Tools
- Rightsize VMs
- Sustained Use Discounts
- Committed Use Discounts
- Preemptible VMs
- Cloud Storage Classes and Lifecycle Policies
- Free Tier Products

### What to do after identifying costs
#### Use tools to reduce them
- Multiple tools and services included in GCP to help bring costs down
Many of them are free

### Rightsizing VMs
#### Why rightsize
- Paying for more compute than needed = wasted money
- Scope machine type to match resource usage (up or down)
#### How it works 
- GCP recommends sizing changes based on metrics from previous 8 days
- Recommends resizing machine type up or down to better fit actual usage
- Properly sized machine = more efficient usage = money saved
- Recommendations automatically generated 
- Note: must restart instance to change machine type

### Sustained Use Discounts
#### Another automatic feature!
- Long running compute(CPU/RAM) automatically discounted

#### How it works 
- When you use a set amount of compute for at least 25% of the month, discount is applied
- Compute does not need to be used on same instances
- Calculations for sustained usage across all mix and match scenarios automatically applied
- Discounts up to 30% of monthly costs
- Note: does not overlap with committed use discounts



### Committed Use Discount
#### Commit to a set amount of compute = save $$$!
- Save up to 70%

#### How it works
- 1- or 3-year commitments
- Purchase set amount of compute/GPU/local SSD's at discount
- Billed monthly, but billed whether or not you use committed resources
Exammple: 1 year commmitted use contract for 8 CPU's/32 GB of RAM
  - CPU/RAM can be used in single instances, or spread across multiple
  - Delete one instance and create another using same specs
- Committed use resources not eligible for sustained use discount - but resources above commmitted amount are eligible

### Preemtible VMs
#### Short-lived, low-cost VMs at huge discounts
- Save up to 80%

#### How it works
- Most instances have option for preemptible VM
- Preemptible VMs can be shut down (i.e. preempted) at any time when capacity is needed
- Max life of 24 hours, even if not preempted
- Not ideal for critical servers
- Ideal for fault-tolerant batch computing workloads
- "Throw a bunch of cheap, disposable VM's at a problem"


## Cloud Storage Classes
#### Object-level availability based on access needs

#### How it works
- Default storage class has no restrictions on availability, modification, or retrieval access, but comes at highest cost
- i.e. 'hot' data
- Lower prices classes restrict how often data can be modified or retrieved without an additional fee
- Ideal for infrequently accessed data (e.g. backups, archive data)
- Classes = Standard, Nearline, Coldline, Archive
- The cheaper the class, the longer it needs to 'sit' before modifying without a fee
- Retrieving data also incurs a fee (higher with cheaper classes)

### Cloud Storage Lifecycle Policies
#### Automatically delete/change class of old data
#### How it works
- Old data that is no longer frequently access shouldn't remain in standard class (or exist at all)
- Solution: automatically delete or change class based on age (or other factors)
- Don't need to manually think about how old each object is before taking action, lifecycle policies do it for you 

### Free Tier Resources
#### Always free, never expires
#### What is it?
- Low usage of many GCP services is always free
- Usage above free tier amounts charged normally
- Great for low usage of services for testing/low traffic workloads
- Examples:
  - 1 free f1-micro GCE instance per month
  - 5 GB Cloud Storage space used per month
  - 1TB BigQuery queries per month

