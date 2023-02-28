### Setup a network and http load balancers
- - -
### Authorize the shell.

<pre><code>gcloud auth list</code></pre>

### List Projects

<pre><code>
gcloud config list project 
</code></pre>
set default region / zone 
<pre><code>
gcloud config set compute/region
gcloud config set compute/zone
</code></pre>

### Creating instances  
<pre><code>
gcloud compute instances create $NAME \
 --zone= \
 --tags=network-lb-tag \
 --machine-type=e2-small \
 --image-family=debian-11 \
 --image-project=debian-cloud \
 --metadata=startup-script='#!/bin/bash
    apt-get update
    apt-get install apache2 -y
    service apache2 restart
    echo "<h3>Web Name: $NAME</h3>" | tee /var/www/html/index.html'
</code></pre>

### Create firewall rule  
<pre><code>
gcloud compute firewall-rules create www-firewall-network-lb \
  --target-tags network-lb-tag --allow tcp:80
</code></pre>

### Get External IP
<pre><code>
gcloud compute instances list 
</code></pre>

curl external ip to see html content

## CONFIGURE THE LOAD BALANCING SERVICE 
### Create the External IP
<pre><code>
gcloud compute addresses create network-lb-ip-1 --region
</code></pre>

### Add Legacy HTTP Heath check Resource
<pre><code>
gcloud compute http-health-checks create basic-check
</code></pre>

### Add target pool in region with health check
<pre><code>
gcloud compute target-pools create www-pool \
 --region --http-health-check basic-check
</code></pre>

### Add the Instances to the Pool
<pre><code>
gcloud compute target-pools add-instances www-pool \
 --instances $NAME, $NAME, etc
</code></pre>

### Add Forwarding Rule
<pre><code>
gcloud compute forwarding-rules create www-rule \
 --region \
 --ports 80 \
 --address network-lb-ip-1 \
 --target-pool www-pool
</code></pre>

## SEND TRAFFIC TO YOUR INSTANCES
### view external ip address of the www-rule forwarding rule 
<pre><code>
gcloud compute forwarding-rules describe www-rule --region
</code></pre>
### Access the External IP as environment variable 
<pre><code>
IPADDRESS=$(gcloud compute forwarding-rules describe www-rule --region --format="json" | jq -r .IPAddress)
</code></pre>

### Show the external IP
<pre><code>
echo $IPADDRESS
</code></pre>

### Curl the IP to verify it's using the host pool
<pre><code>
while true; do curl -m1 $IPADDRESS; done
</code></pre>

## CREATE AN HTTP LOAD BALANCER

<pre><code>
gcloud compute instance-templates create lb-backend-template \
 --region= \
 --network=default \
 --subnet=default \
 --tags=allow-health-check \
 --machine-type=e2-medium \
 --image-family=debian-11 \
 --image-project=debain-cloud \
 --metadata=startup-script='#!/bin/bash
  apt-get update
  apt-get install apache2 -y
  a2ensite default-ssl
  a2enmod ssl
  vm_hostname=$(curl -H "Metadata-Flavor:Google" \

http://169.254.169.254/computeMetadata/v1/instance/name)"
echo "Page served from: $vm_hostname" | \
  tee /var/www/html/index.html
  systemctl restart apache2'
</code></pre>

### Create MIG(Managed Instance Group) based on the template
<pre><code>
gcloud compute instance-groups managed create lb-backend-group \
 --template=lb-backend-template --size=2 --zone=
</code></pre>

### Create the fw-allow-health-check Firewall Rule
<pre><code>
gcloud compute firewall-rules create fw-allow-health-check \
 --network=default \
 --action=allow \
 --direction=ingress \
 --source-range=130.211.0.0/22,35.191.0.0/16 \ (Google Cloud health checking systems)
 --target-tags=allow-health-check \
 --rules=tcp:80
</code></pre>

### Set Global Static External IP for LoadBalancer
<pre><code>
gcloud compute addresses create lb-ipv4-1 \
 --ip-version=IPV4 \
 --global
</code></pre>
### Note the ip that was reserved
<pre><code>
gcloud compute addresses describe lb-ipv4-1 \
 --format="get(address)" \
 --global
</code></pre>

### Create a Health Check for the Load Balancer
<pre><code>
gcloud compute health-checks create http http-basic-check \
 --port 80
</code></pre>
### Create backend service
<pre><code>
gcloud comopute backend-services create web-backend-service \
 --protocol=HTTP \
 --port-name=http \
 --health-checks=http-basic-check \
 --global
</code></pre>
### Add instance to group as backend to the backend service 
<pre><code>
gcloud compute backend-services add-backend web-backend-service \
 --instance-group=lb-backend-group \
 --instance-group-zone = \
 --global
</code></pre>

### create url map to route request to backend 
<pre><code>
gcloud compute url-maps create web-map-http \
 --default-service web-backend-service
</code></pre>

### Create target HTTP proxy to route quests to url map
<pre><code>
gcloud compute target-http-proxies create http-lb-proxy \
 --url-map web-map-http
</code></pre>
### Create global forwarding rule to route incoming request to the proxy
<pre><code>
gcloud compute forwarding-rules create http-content-rule \
 --address=lb-ipv4-1 \
 --global \
 --target-http-proxy=http-lb-proxy \
 --ports=80
</code></pre>

## TEST TRAFFIC TO THE INSTANCE