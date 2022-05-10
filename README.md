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

![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.02.59%20PM.png)


# Update the guestbook
Use the Explorer to edit index.html in the public directory. Let's edit the title to be more specific. On line number 12, that says <h1>Guestbook - v1</h1>, change it to include your name. Something like <h1>Alex's Guestbook - v1</h1>.

docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 && docker push us.icr.io/$MY_NAMESPACE/guestbook:v1. - Build and push the app again using the same tag. This will overwrite the previous image.


oc import-image guestbook:v1 --from=us.icr.io/$MY_NAMESPACE/guestbook:v1 --confirm

Click Builds > Image Streams in the navigation from administrator in openshift console and check in history for edits. Return to the Developer perspective. View the guestbook in the browser again.  You should see your new title on this page! OpenShift imported the new version of our image, and since the Deployment points to the image stream, it began running this new version as well.


# Guestbook storage

From the guestbook in the browser, click the /info link beneath the input box. This is an information endpoint for the guestbook. Return to the guestbook application in the browser by clicking the Route location again. You should see that your previous entries appear no more. This is because the guestbook was restarted when your update was deployed in the last section. We need a way to persist the guestbook entries even after restarts.

![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.03.19%20PM.png)

# Delete the guestbook
In order to deploy a more complex version of the guestbook, delete this simple version.

# Deploy Redis master and slave

## This application uses the v2 version of the guestbook web front end and adds on 1) a Redis master for storage, 2) a replicated set of Redis slaves, and 3) a Python Flask application that calls a Watson Natural Language Understanding service deployed in IBM Cloud to analyze the tone. For all of these components, there are Kubernetes Deployments, Pods, and Services. One of the main concerns with building a multi-tier application on Kubernetes is resolving dependencies between all of these separately deployed components.

## In a multi-tier application, there are two primary ways that service dependencies can be resolved. The v2/guestbook/main.go code provides examples of each. For Redis, the master endpoint is discovered through environment variables. These environment variables are set when the Redis services are started, so the service resources need to be created before the guestbook Pods start. For the analyzer service, an HTTP request is made to a hostname, which allows for resource discovery at the time when the request is made. Consequently, we'll follow a specific order when creating the application components. First, the Redis components will be created, then the guestbook application, and finally the analyzer microservice.


oc apply -f redis-master-deployment.yaml  - create redis master deployment
oc get deployments
oc get pods. 

oc apply -f redis-master-service.yaml.  - create redis master service
oc apply -f redis-slave-deployment.yaml  - create redis slave deployment 
oc apply -f redis-slave-service.yaml.   - create redis slave service

![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.11.16%20PM.png)

# Deploy v2 guestbook app

Click the +Add button to add a new application to this project in OpenShift. Click the From Dockerfile option. Paste the below URL in the Git Repo URL box. - https://github.com/ibm-developer-skills-network/guestbook . leave the rest and click on create. Since we gave OpenShift a Dockerfile, it will create a BuildConfig and a Build that will build an image using the Dockerfile, push it to the internal registry, and use that image for a Deployment. In the Resources tab, click the Route location to load the guestbook in the browser. Notice that the header says "Guestbook - v2" instead of "Guestbook - v1". From the guestbook in the browser, click the /info link beneath the input box. Notice that it now gives information on Redis since we're no longer using the in-memory datastore.

![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.12.29%20PM.png)

# Login to IBM CLOUD

We need to store these credentials in a Kubernetes secret in order for our analyzer microservice to utilize them.

ibmcloud login -u <your_email_address> 

ibmcloud target -g <resource_group>

Use the Explorer to edit binding-hack.sh. The path to this file is guestbook/v2/binding-hack.sh. You need to insert the name of your IBM Cloud Natural Language Understanding service where it says <your nlu service name>. You need to insert your OpenShift project where it says <my_project>
  
Run the script to create a Secret containing credentials for your Natural Language Understanding service.
./binding-hack.sh.
  
# Deploy the analyzer microservice 
  
docker build . -t us.icr.io/$MY_NAMESPACE/analyzer:v1 && docker push us.icr.io/$MY_NAMESPACE/analyzer:v1. - build and push analyzer image

  oc apply -f analyzer-deployment.yaml - create analyzer deployment
  oc apply -f analyzer-service.yaml- create analyzer service

  ![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.24.52%20PM.png)
  
  
Return to the guestbook in the browser, refresh the page, and submit a new entry. You should see your entry appear along with a tone analysis.
![](https://github.com/saiswaruprath/ibmkubernetes/blob/main/images/Screen%20Shot%202022-05-07%20at%201.27.17%20PM.png)  
  
  
  



