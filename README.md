## Welcome to GCP DevOps Demo Project 

This is a practice demonstration for this article 
- https://medium.com/p/8d8fafffff83/edit 

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
