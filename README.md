## A Fully Serverless DevOps Stack on GCP!
![alt text](https://github.com/hamza-labs/gcp-devops-demo/blob/main/img/devops-stack.png?raw=true)

### Step 1: Code 
```
- In this article, we gonna use the famous Petclinic Spring Boot Application to demonstrate different Google Cloud Services. 
Clone the DevOps Repo (Google Cloud Repository)
Open your Google Cloud Shell Editor --> New Terminal  
cd && mkdir projects && cd projects
gcloud source repos clone gcp-devops-demo --project=hamza-labs-1
cd gcp-devops-demo
git add . && git commit -m "Commit Code" && git push origin master
```

### Step 2-a : Build locally and Test
```
Option A: Build and Test you app locally using Gradle
cd ~/projects/gcp-devops-demo
./gradlew build -x test  
./gradlew bootRun
curl localhost:8080
```

```
Option B: Build and Test you app locally using Cloud Native Buildpacks and Docker!
./gradlew bootBuildImage --imageName=petclinic
docker images 
docker run -p 8080:8080 -t petclinic 
docker ps 
```

### Step 2-b: Build your App using Cloud Build & Store it in Artifact Registry
```
// Creating the Artifact Registry 
gcloud artifacts repositories create petclinic --repository-format=docker --location=us-central1 --description="Docker repo for petclinic" 
gcloud artifacts repositories list
```

```
// Building the App Using Cloud Build and Pushing it to Artifact Registry! 
gcloud builds submit --pack image=us-central1-docker.pkg.dev/hamza-labs-1/petclinic/petclinic
```

### Step 3: Creating a build trigger (To connect Cloud Source Repo + Cloud Build + Artifact Registry)
```
gcloud beta builds triggers create cloud-source-repositories --repo=gcp-devops-demo --branch-pattern=master  --build-config=Buildpacks
```

```Or 
- Go to https://console.cloud.google.com/artifacts
- Create a trigger with a name ci-trigger
```

### Step 4: Deploy our App to Cloud Run 
```
gcloud run deploy petclinic --image us-central1-docker.pkg.dev/hamza-labs-1/petclinic/petclinic:latest --platform managed --region us-central1 --allow-unauthenticated
```
```
Or 
- Go to https://console.cloud.google.com/artifacts
- Click on Deploy and Choose Cloud Run
```

```
Optional if you wanna map your cloud run services to a domain 
gcloud domains list-user-verified
gcloud domains verify hamza.cloud
gcloud beta run domain-mappings create --service petclinic --domain hamza.cloud
gcloud beta run domain-mappings describe --domain hamza.cloud
```

### Step 5: Log, Monitor and Trace your Services
```
To explore Monitoring and Logging advanced features, please check these links
- Go to https://console.cloud.google.com/run/detail/us-central1/petclinic/logs
More about Monitoring
-  https://cloud.google.com/run/docs/monitoring
```

### Step 6: Blocking docker images deployment outside Cloud Build 

```
// Run this to be able to pull or push inages from Artifact Registry
gcloud auth configure-docker us-central1-docker.pkg.dev
// Tag the Petclinic image, and Push to Artifact Registry 
docker tag petclinic us-central1-docker.pkg.dev/hamza-labs-1/petclinic/petclinic-bin-auth:latest
docker push us-central1-docker.pkg.dev/hamza-labs-1/petclinic/petclinic-bin-auth:latest
// Deploy the new image to Cloud Run
gcloud run deploy petclinic-bin-auth --image us-central1-docker.pkg.dev/hamza-labs-1/petclinic/petclinic-bin-auth:latest --platform managed --region us-central1 --allow-unauthenticated
```

```
// For more about how to use Binary Authorization and Container registry scanning
- Go To https://cloud.google.com/binary-authorization
- Go To https://cloud.google.com/container-registry/docs/container-analysis
```

### Useful command line for cleaning 
```
docker rm -f $(docker ps -aq)
docker rmi $(docker images -a -q)
```

