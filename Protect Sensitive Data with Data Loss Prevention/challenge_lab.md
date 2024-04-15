### Tips:  
Variables inside <> need to be changed with the ones that the lab will give you  
If the commands fail for some reason such as "invalid format/syntax" try to write all the command without copypasting into Cloud Shell

# Task 1 - Redact sensitive data from text content
```
# Export PROJECT_ID for future use
export PROJECT_ID=$DEVSHELL_PROJECT_ID

# Enable DLP API
gcloud services enable dlp.googleapis.com cloudkms.googleapis.com \
--project $PROJECT_ID

# Make a deintify request for a file previously created
curl -s \
  -H "Authorization: Bearer $(gcloud auth print-access-token)" \
  -H "Content-Type: application/json" \
  https://dlp.googleapis.com/v2/projects/$PROJECT_ID/content:deidentify \
  -d @redact-request.json -o redact-response.txt

# Copy output file to bucket
gsutil cp redact-response.txt gs://<BUCKET_NAME>
```
# Task 2 - UI
# Task 3 - UI
For the inspect part download the buckets content after the run has finished just to be sure.