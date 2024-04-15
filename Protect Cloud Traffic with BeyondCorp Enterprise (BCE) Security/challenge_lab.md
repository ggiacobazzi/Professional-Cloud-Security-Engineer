### Tips:  
Variables inside <> need to be changed with the ones that the lab will give you  
If the commands fail for some reason such as "invalid format/syntax" try to write all the command without copypasting into Cloud Shell

# Task 1 - Deploy a provided web application
```
# Clone repository
git clone https://github.com/GoogleCloudPlatform/python-docs-samples.git

cd python-docs-samples/appengine/standard_python3/hello_world/

# Deploy app
export REGION=<REGION>

gcloud app create --region $REGION

gcloud app deploy
```

# Task 2 - Configure OAuth Consent for the web application deployed
```
# Configure OAuth consent screen
Go to Security > Identity Aware Proxy > Enable API > Go to IAP > Configure OAuth Consent Screen > Set as required
```

# Task 3 - Configure the deployed web application to utilize IAP to protect traffic
```
# Activate IAP
Go to Security > Identity Aware Proxy > Click on the toggle on the App Engine row

# Set correct roles for tester account
Click on the App Engine Resource > Add Principal > Copy the tester email and set "IAP-secured Web App User" as its role
```