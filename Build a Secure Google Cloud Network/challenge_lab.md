### Tips:  
Variables inside <> need to be changed with the ones that the lab will give you  
If the commands fail for some reason such as "invalid format/syntax" try to write all the command without copypasting into Cloud Shell

# Task 1 - Remove permissions
```
# Remove overly permissioned rule
gcloud compute firewall-rules delete open-access
```
# Task 2 - Start bastion vm
```
gcloud compute instances start bastion --zone <ZONE> --project $DEVSHELL_PROJECT_ID
```
# Task 3 - Create firewall rule IAP to bastion vm
```
# Create firewall rule
gcloud compute firewall-rules create ssh-ingress --allow=tcp:22 --source-ranges 35.235.240.0/20 --target-tags <NETWORK_TAG_IAP> --network acme-vpc

# Add firewall rule to vm
gcloud compute instances add-tags bastion --tags=[NETWORK TAG-1] --zone=<ZONE>
```
# Task 4 - Create firewall rule HTTP to juice vm
```
# Create firewall rule
gcloud compute firewall-rules create http-ingress --allow=tcp:80 --source-ranges 0.0.0.0/0 --target-tags <NETWORK_TAG_HTTP> --network acme-vpc

# Add firewall rule to vm
gcloud compute instances add-tags juice-shop --tags=<NETWORK_TAG_HTTP> --zone=us-central1-b
```
# Task 5 - Create firewall rule from network SSH bastion -> juice vm
```
# Get IP range of subnet "acme-mgmt-subnet"
export IP=$(gcloud compute networks subnets describe <SUBNET_NAME> --region=<REGION> --format="get(ipCidrRange)")

# Create firewall rule
gcloud compute firewall-rules create internal-ssh-ingress --allow=tcp:22 --source-ranges $IP --target-tags <NETWORK_TAG_SSH> --network acme-vpc

# Add firewall rule to vm
gcloud compute instances add-tags juice-shop --tags=<NETWORK_TAG_SSH> --zone=<ZONE>

# SSH into bastion vm
gcloud compute ssh --zone <ZONE> "bastion" --tunnel-through-iap --project $DEVSHELL_PROJECT_ID

# SSH from bastion vm to juice-shop vm
## Execute into bastion vm!
export INTERNAL_IP=$(gcloud compute instances describe <INSTANCE_NAME> --zone=<ZONE> --format='get(networkInterfaces[0].networkIP)')

ssh $INTERNAL_IP
```