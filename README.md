## Welcome to this quick demo used to demonstrate the Google Cloud DevOps Toolchain! 


## Option A; Clone and Test your Petclinic app locally with the in-memory HyperSQL database.
```
git clone 
cd petclinic-demo
./gradlew bootRun
curl localhost:8080
```
## Option B; Clone and Test your Petclinic app locally using Docker with the in-memory HyperSQL database.
```
git clone 
cd petclinic-demo
./gradlew bootBuildImage --imageName=petclinic-demo
docker images 
docker run -p 8080:8080 -t petclinic-demo
docker ps 
```

## How to Create your First Google Cloud Repository
```
gcloud source repos create petclinic-demo
cd ~/projects/
gcloud source repos clone petclinic-demo
git add .
git commit -m "First Push"
git push origin master
```

## Create your Cloud SQL Instance for the Petclinic Demo App
```
./gradlew build
gcloud sql instances create pet-clinic-mysql-instance
gcloud sql databases create petclinic --instance pet-clinic-mysql-instance
gcloud sql instances describe pet-clinic-mysql-instance | grep connectionName
hamza-labs-1:us-central1:pet-clinic-mysql-instance
```

## Configure your Spring Boot App to Use Cloud SQL (MYSQL)
- Update your Gradle Configuration 
```

```

- Update your application-mysql.properties
```

```

## Create your GKE Autopilot Plateform 
```
gcloud container --project "hamza-labs-1" clusters create-auto "gke-autopilot-cluster" --region "us-central1" --release-channel "regular" --network "projects/hamza-labs-1/global/networks/default" --subnetwork "projects/hamza-labs-1/regions/us-central1/subnetworks/default" --cluster-ipv4-cidr "/17" --services-ipv4-cidr "/22"
```

## Build your Artificats using Cloud Build 
```
gcloud artifacts repositories create petclinic-demo-repo --repository-format=maven --location=us-central1
gcloud iam service-accounts create artifact-registry-sa --description="sa-for-artifact-registry" --display-name="SA_AR"
gcloud artifacts repositories add-iam-policy-binding petclinic-demo-repo --location=us-central1 --member=serviceAccount: 	 --role=roles/artifactregistry.writer
gcloud artifacts repositories add-iam-policy-binding petclinic-demo --location=us-central1 --member=serviceAccount:artifact-registry-sa@hamza-labs-1.iam.gserviceaccount.com --role=roles/artifactregistry.writer


```

## Store Your artifacts in Google Artifact Registry 
```
```

## Deploy your app in GKE Autopilot using Cloud Deploy
```
```

## Operate your app using Cloud Operations
```
```
