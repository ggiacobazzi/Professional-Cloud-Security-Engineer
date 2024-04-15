### Tips:  
Variables inside <> need to be changed with the ones that the lab will give you  
If the commands fail for some reason such as "invalid format/syntax" try to write all the command without copypasting into Cloud Shell

# Task 1 - Configure Cloud Monitoring
```
# Export PROJECT_ID for future use
export PROJECT_ID=$DEVSHELL_PROJECT_ID

# Check if Monitoring API is already active
gcloud services list --project=PROJECT_ID

# If not, run this command
gcloud services enable monitoring --project=PROJECT_ID
```

# Task 2 - Configure a Compute Instance to generate Custom Cloud Monitoring metrics
````
# setup the environment variables
PROJECT_ID=$DEVSHELL_PROJECT_ID
INSTANCE_ID=$(gcloud compute instances describe "video-queue-monitor" --zone="us-east1-b" --format="value[](id)")
INSTANCE_ZONE=<ZONE>

# retrieve the original start-script.sh and replace content with environment variables
gcloud compute instances describe "video-queue-monitor" \
    --zone=$INSTANCE_ZONE \
    --format="value[](metadata.items.startup-script)" \
    > "start-script.sh"

sed "s/\[REPLACE-WITH-PROJECT_ID\]/$PROJECT_ID/g" -i start-script.sh
sed "s/\[REPLACE-WITH-INSTANCE-ID\]/$INSTANCE_ID/g" -i start-script.sh
sed "s/\[REPLACE-WITH-INSTANCE-ZONE\]/$INSTANCE_ZONE/g" -i start-script.sh

# update the metadata
gcloud compute instances add-metadata "video-queue-monitor" \
    --zone=$INSTANCE_ZONE \
    --metadata-from-file="startup_script"="start-script.sh"

# Restart VM
gcloud compute instances start "video-queue-monitor" --zone=$INSTANCE_ZONE