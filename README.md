## Welcome to GCP DevOps Demo Project 
The GCP DevOps Demo project is a step by step guide to test DevOps Stack detailled in this article. 
- https://medium.com/p/8d8fafffff83/edit 

## Let's first clone the repo and test it locally 
```
In this Demonstration, we will be using the famous pet clinic Java Spring Boot Application. 
git clone https://github.com/hamza-labs/pdf-demo.git  

Create your GCP project  
Let's create a GCP project, (required to use Google Cloud, and forms the basis for creating, enabling, and using all Google Cloud services, managing APIs, enabling billing, adding and removing collaborators, and managing permissions) 
Go to console.google.com
Open your Cloud Shell 
gcloud init 
Create a new configuration 
Choose your email and create a project using this command line
gcloud project create gcp-developer-workflow
Create your git Repo using Google Cloud Source Repositories
Let's create a git repo using Cloud Source Repositories service, a single place to store, manage, and track code.
gcloud source repos create gcp-developer-workflow
mkdir projects && cd projects 
gcloud source repos clone gcp-developer-workflow --project=gcp-developer-workflow
cd gcp-developer-workflow
go mod init example.com/helloworld
nano helloworld.go 
git add . && git commit -m "adding my first go app file"
git push -u origin master
Cloud Build for Continuous integration and continuous delivery
First, let's configure Cloud Build to build and store Docker images. 
cd projects/gcp-developer-workflow
gcloud config get-value project
gcloud builds submit --tag gcr.io/gcp-developer-workflow/helloworld .


```

## Using Docker! and run the application Locally
```
docker build . -t pdf
docker run -p 8080:8080 pdf
http://localhost:8080
```

## Let's now build and push the container image to Google Cloud Container registry using Cloud Build
```
gcloud builds submit -t gcr.io/hamza-labs-332005/pdf	 
gcloud container images list --repository=gcr.io/hamza-labs-332005
```

## Now let's deploy the app to Cloud Run manually (no CI/CD)
``` 
gcloud beta run deploy --image gcr.io/hamza-labs-332005/pdf
```

## Let's create a Trigger in Cloud Build and connect with GitHub 
console.cloud.google.com
