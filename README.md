# ibmkubernetes
Task 1: Deploy a simple v1 guestbook application. 

Task 2: Use an in-memory data store for the simple guestbook application. 

Task 3: Update the simple guestbook homepage to include your name. 

Task 4: Automatically deploy the homepage update using a second image stream tag. 

Task 5: Deploy the second version of the guestbook application using an OpenShift build. 

Task 6: Deploy a Redis master, a Redis slave, and an analyzer microservice.

Task 7: Use Redis for the v2 guestbook application instead of an in-memory datastore. 

Task 8: Submit entries to the guestbook and have their tone analyzed. Some simple sentences will not have a tone detected. Ensure that you submit something complex enough so that its tone is detected.

Task 9: Create a Horizontal Pod Autoscaler that shows guestbook as the scale target, the current and desired replicas as three, and the last scale time as the time the deployment was scaled up to three replicas 



To start, you will deploy a simple guestbook application that uses in-memory storage to retain the guestbook entries. Afterward, you will deploy a multi-tier web application that consists of a web front end, a Redis master and replicated slaves for storage, as well as an analyzer service that analyzes the tone of entries left in the guestbook. In order to utilize the analyzer service, you will have to create a free instance of Tone Analyzer in your personal IBM Cloud account.





# Project Overview
## Guestbook application
Guestbook is a simple, multi-tier web application that we will build and deploy with Docker and Kubernetes. The application consists of a web front end, a Redis master for storage, a replicated set of Redis slaves, and a Natural Language Understanding service that will analyze the tone of the comments left in the guestbook. For all of these we will create Kubernetes Deployments, Pods, and Services.

There are two versions of this application. Version 1 (in the v1 directory) is the simple application itself, while version 2 (in the v2 directory) extends the application by adding additional features that leverage the Watson Natural Language Understanding service.

We will deploy and manage this entire application on OpenShift.


# Build the guestbook app
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1   - build the guestbook app

docker push us.icr.io/$MY_NAMESPACE/guestbook:v1. - push the image to ibmcloud container registry


# Deploy guestbook app from the OpenShift internal registry

oc tag us.icr.io/$MY_NAMESPACE/guestbook:v1 guestbook:v1 --reference-policy=local --scheduled  -  Create an image stream that points to your image in IBM Cloud Container Registry. With the --reference-policy=local option, a copy of the image from IBM Cloud Container Registry is imported into the local cache of the internal registry and made available to the cluster's projects as an image stream. The --schedule option sets up periodic importing of the image from IBM Cloud Container Registry into the internal registry. The default frequency is 15 minutes

Open the OpenShift web console using the link at the top of the lab environment. From the Developer perspective, click the +Add button to add a new application to this project. Click the Container Image option so that we can deploy the application using an image in the internal registry.
Under Image, switch to "Image stream tag from internal registry". Select your project, and the image stream and tag you just created (guestbook and v1, respectively). You should have only have one option for each of these fields anyway since you only have access to a single project and you only created one image stream and one image stream tag. Keep all the default values and hit Create at the bottom. This will create the application and take you to the Topology view. From the Topology view, click the guestbook Deployment. This should take you to the Resources tab for this Deployment, where you can see the Pod that is running the application as well as the Service and Route that expose it. Click the Route location (the link) to view the guestbook in action. Try out the guestbook by putting in a few entries. You should see them appear above the input box after you hit Submit.


# Update the guestbook




