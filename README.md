# Welcome to this quick GCP DevOps Toolchain Demo! 

<img src="https://github.com/hamza-labs/gcp-devops-demo/blob/main/src/main/resources/static/resources/images/gcp-devops-stack.png?raw=true" width="650">

### Step 1: Code and Clone your Repo! 

- Clone the DevOps Repo (Google Cloud Repository)
```
Open your Cloud Shell Editor shell.cloud.google.com 
New Terminal 
cd && mkdir projects && cd projects
gcloud source repos clone gcp-devops-demo --project=hamzalabs
cd gcp-devops-demo
git add . && git commit -m "Commit Code" && git push google master
```

- Create your Cloud SQL MySQL Instance if you wanna test the app with a DB 
```
gcloud sql instances create petclinic-mysql-instance
gcloud sql databases create petclinic-db --instance petclinic-mysql-instance
gcloud sql instances describe petclinic-mysql-instance | grep connectionName 
```

- And Don't forget to edit your /src/main/resources/application.propreties

### Step 2-a : Build locally and Test

- Option A: Build and Test you app locally using Gradle
```
cd ~/projects/gcp-devops-demo
./gradlew build -x test  
./gradlew bootRun
curl localhost:8080
```

- Option B: Build and Test you app locally using Cloud Native Buildpacks and Docker!
```
./gradlew bootBuildImage --imageName=petclinic
docker images 
docker run -p 8080:8080 -t petclinic 
docker ps 
```

- Optional: Verifiy Data persistance 
```
gcloud sql connect petclinic-mysql-instance -u root
mysql> use petclinic-db;
mysql> select * from owners;
The Spring Cloud GCP starter uses a special SSL socket factory to secure connection to the Cloud SQL db server
```

### Step 2-b: Build your App using Cloud Build & Store it in Artifact Registry

- Creating the Artifact Registry Repository! 
```
gcloud artifacts repositories create petclinic-artifact-repo --repository-format=docker --location=us-central1 
gcloud artifacts repositories list
```
- Create a config file to build your App Using Cloud Build and Pushing it to Artifact Registry! 

```
nano cloudbuild.yaml // insert this code for the build and the push part 
```

```
steps:

# Build your app using Cloud Build and Buildpacks.io & Push it to Google Artifact Registry! 
- name: 'gcr.io/k8s-skaffold/pack'
  entrypoint: 'pack'
  args: ['build','--builder=gcr.io/buildpacks/builder', 
  '--publish', 'us-central1-docker.pkg.dev/hamzalabs/petclinic-artifact-repo/petclinic:latest']
```

```
gcloud builds submit --config=cloudbuild.yaml
Verify on the GCP console your build and the Artifact Registry Repository! 
```

### Step 4: Deploy our App to Cloud Run using CloudBuild 
- Create a Service Account petclinic with Cloud SQL Client Permission! 
```
gcloud iam service-accounts create SERVICE_ACCOUNT_ID \
    --description="DESCRIPTION" \
    --display-name="DISPLAY_NAME"

gcloud projects add-iam-policy-binding PROJECT_ID \
    --member="serviceAccount:SERVICE_ACCOUNT_ID@PROJECT_ID.iam.gserviceaccount.com" \
    --role="cloudsql.instances.connect"
```

- Edit your Cloudbuild.yaml to add the Deploy step! 
```
nano cloudbuild.yaml // insert this code to deploy the app using cloud build to cloud Run
```

```
# deploy to Google Cloud Run
- name: 'gcr.io/cloud-builders/gcloud'
  args: ['run', 'deploy', 'petclinic', '--image', 'us-central1-docker.pkg.dev/hamzalabs/petclinic-artifact-repo/petclinic:latest',
  '--region', 'us-central1', '--allow-unauthenticated', 
   '--cpu=2', '--memory=512M',
  '--service-account', 'SERVICE_ACCOUNT_ID@PROJECT_ID.iam.gserviceaccount.com', 
  '--add-cloudsql-instances=hamzalabs:your_petclinic_mysql-connection_name']
```

```
gcloud builds submit --config=cloudbuild.yaml
Verify on the GCP console if the app is depoyed to Cloud Run
```

### Step 4: Automate your CI/CD with a Cloud Build Trigger!
```
gcloud beta builds triggers create cloud-source-repositories --repo=gcp-devops-demo --branch-pattern=master  --build-config=cloudbuild.yaml
Edit the README and commit changes!
git add . && git commit -m "Commit Code" && git push google master
```

### Step 5: Log, Monitor and Trace your Services
- To explore metrics, Logs and Error reporting! 
```
- https://console.cloud.google.com/run/detail/us-central1/petclinic/logs
- https://console.cloud.google.com/run/detail/us-central1/petclinic/metrics
- https://console.cloud.google.com/errors 
```

### Useful command line for cleaning 
```
docker rm -f $(docker ps -aq)
docker rmi $(docker images -a -q)
```
