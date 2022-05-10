# ibmkubernetes
Task 1: Deploy a simple v1 guestbook application. (2 points)

Task 2: Use an in-memory data store for the simple guestbook application. (1 point)

Task 3: Update the simple guestbook homepage to include your name. (1 point)

Task 4: Automatically deploy the homepage update using a second image stream tag. (1 point)

Task 5: Deploy the second version of the guestbook application using an OpenShift build. (5 points)

Task 6: Deploy a Redis master, a Redis slave, and an analyzer microservice.(3 points)

Task 7: Use Redis for the v2 guestbook application instead of an in-memory datastore. (1 point)

Task 8: Submit entries to the guestbook and have their tone analyzed. Some simple sentences will not have a tone detected. Ensure that you submit something complex enough so that its tone is detected.(2 points)

Task 9: Create a Horizontal Pod Autoscaler that shows guestbook as the scale target, the current and desired replicas as three, and the last scale time as the time the deployment was scaled up to three replicas (4 points)



To start, you will deploy a simple guestbook application that uses in-memory storage to retain the guestbook entries. Afterward, you will deploy a multi-tier web application that consists of a web front end, a Redis master and replicated slaves for storage, as well as an analyzer service that analyzes the tone of entries left in the guestbook. In order to utilize the analyzer service, you will have to create a free instance of Tone Analyzer in your personal IBM Cloud account.





Project Overview
Guestbook application
Guestbook is a simple, multi-tier web application that we will build and deploy with Docker and Kubernetes. The application consists of a web front end, a Redis master for storage, a replicated set of Redis slaves, and a Natural Language Understanding service that will analyze the tone of the comments left in the guestbook. For all of these we will create Kubernetes Deployments, Pods, and Services.

There are two versions of this application. Version 1 (in the v1 directory) is the simple application itself, while version 2 (in the v2 directory) extends the application by adding additional features that leverage the Watson Natural Language Understanding service.

We will deploy and manage this entire application on OpenShift.
