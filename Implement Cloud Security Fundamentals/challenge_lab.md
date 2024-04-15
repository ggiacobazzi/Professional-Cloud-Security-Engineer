### Tips:  
Variables inside <> need to be changed with the ones that the lab will give you  
If the commands fail for some reason such as "invalid format/syntax" try to write all the command without copypasting into Cloud Shell

## Task 1 - Create a custom role:
```
# Create custom_role.yaml
gcloud iam roles create <Custom Security Role> --project $DEVSHELL_PROJECT_ID \
--file custom_role.yaml
```

## Task 2 - Create a service account:
```
gcloud iam service-accounts create <Service Account> --display-name <Service Account>
```

## Task 3 - Bind sa with custom roles and built-in roles required:
```
# To fix to have more roles because with this one only one role is taken I think, or if it doesn't work just # add the missing roles from the UI
gcloud projects add-iam-policy-binding $DEVSHELL_PROJECT_ID \
    --member serviceAccount:orca-sa@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com --role roles/monitoring.viewer --role roles/monitoring.metricWriter \
 --role roles/logging.logWriter --role projects/$DEVSHELL_PROJECT_ID/roles/orca_storage_editor_992/<Custom Role>
```

## Task 4 - Create a private cluster
```
export ZONE=<ZONE REQUIRED>
export REGION=<REGION REQUIRED>
gcloud beta container clusters create orca-cluster-819 \
–-enable-master-authorized-networks \
–-enable-private-nodes \
–-enable-private-endpoint \
–-enable-ip-alias \
–-master-ipv4-cidr 172.16.0.32/28 \
–-network orca-build-vpc \
–-subnetwork orca-build-subnet \
–-zone $ZONE \
–-service-account <Service Account>@$DEVSHELL_PROJECT_ID.iam.gserviceaccount.com

# Grep external IP of the VM
gcloud compute instances describe orca-jumphost --zone=$ZONE | grep natIP

# Authorize that CIDR range on the cluster 
# natIP is the one got from the previous command, not given by the lab itself
gcloud container clusters update <Cluster Name>
 
–-master-authorized-networks <natIP>/32
```

## Task 5 - Create a deployment on the private cluster
```
# SSH into instance
gcloud compute ssh orca-jumphost --zone=$ZONE

# Install gcloud and connect to the cluster
sudo apt-get install google-cloud-sdk-gke-gcloud-auth-plugin
echo "export USE_GKE_GCLOUD_AUTH_PLUGIN=True" >> ~/.bashrc
source ~/.bashrc
-- <Cluster Name> --internal-ip --project=$DEVSHELL_PROJECT_ID --zone <cluster zone>

# Create deployment
kubectl create deployment hello-server --image=gcr.io/google-samples/hello-app:1.0
```