---
title: "Create App - Application Lifecycle"
date: 2023-10-23T22:03:10+02:00
draft: false
weight: 201
---

In the previous lab, you explored the Cluster Lifecycle functionality in RHACM. This allowed you to register/import an OpenShift® cluster to RHACM, which you can now use to deploy applications.

Application Lifecycle functionality in RHACM provides the processes that are used to manage application resources on your managed clusters. This allows you to define a single or multi-cluster application using Kubernetes specifications, but with additional automation of the deployment and lifecycle management of resources to individual clusters. An application designed to run on a single cluster is straightforward and something you ought to be familiar with from working with OpenShift fundamentals. A multi-cluster application allows you to orchestrate the deployment of these same resources to multiple clusters, based on a set of rules you define for which clusters run the application components.

The table below describes the different components that the Application Lifecycle model in RHACM is composed of:


---
|Resource|Purpose|
|--------|-------|
|Channel|Defines a place where deployable resources are stored, such as an object store, Kubernetes namespace, Helm repository, or GitHub repository.|
|Subscription|Defines a set of deployable resources that are to be deployed to a target cluster.|
|Placement(Old PlacementRule API to be deprecated soon)|Defines the target clusters where subscriptions deploy and maintain the application. It is composed of Kubernetes resources identified by the Subscription resource and pulled from the location defined in the Channel resource.|
|Application|A way to group the components here into a more easily viewable single resource. An Application resource typically references a Subscription resource.|
---

These are all Kubernetes custom resources, defined by a Custom Resource Definition (CRD), that are created for you when RHACM is installed. By creating these as Kubernetes native objects, you can interact with them the same way you would with a Pod. For instance, running oc get application retrieves a list of deployed RHACM applications just as oc get pods retrieves a list of deployed Pods.

This may seem like a lot of extra resources to manage in addition to the deployables that actually make up your application. However, they make it possible to automate the composition, placement, and overall control of your applications when you are deploying to many clusters. With a single cluster, it is easy to log in and run oc create -f…​. If you need to do that on a dozen clusters, you want to make sure you do not make a mistake or miss a cluster, and you need a way to schedule and orchestrate updates to your applications. Leveraging the Application Lifecycle Builder in RHACM allows you to easily manage multi-cluster applications.

### Creating an Application

Prerequisites:   

    On the local cluster add a label:  `environment=dev`

![Edit Labels](/images/202_1.png)

    On the new cluster you provisioned via ACM double check you added the label:  `environment=prod`

![Overview Cluster Labels](/images/202_2.png)

In RHACM, navigate to Applications and click Create application and select Subscription.
Enter the following information:

Next to **Create application**, make sure the YAML dial is ON

---
|Name:| book-import|
|Namespace:| book-import|
---

---
|** Under Repository location for resources, select the GIT repository **|
|---|---|
|URL:|  https://github.com/hichammourad/book-import.git|
|Branch:|  master-no-pre-post|
|Path:|  book-import|


![Create APplication](/images/202_3.png)

---
Under the **Select clusters for application deployment**, select **Deploy application resources on clusters with all specified labels**

---
|Cluster sets:| global|
|Label:| environment|
|Value:| dev|
---
![Set label dev](/images/202_4.png)


Click **Create** and after a few minutes you will see the application and all its components available in RHACM.

![app deployed](/images/202_5.png)


 If everything was done correctly you should be able to see the application deployed to local-cluster. Go to Applications, and make sure to filter by subscription as the image below:


![check application subscription](/images/202_6.png)

This will show only the apps deployed from ACM, instead of all the existing apps in the managed clusters. Click on the **book-import** application and have a look at the **topology** view


![topology view](/images/202_7.png)

Select the Route and click on the URL provided, you should see the Book Import application


![topology route](/images/202_8.png)


See the Book Import user interface.

![topology route](/images/202_9.png)


Feel free to experiment with the application.  Edit it and change the label to environment=prod.  What happens to the application?

You have now completed the overview of the **Application Lifecycle functionality*** in RHACM.

You successfully deployed an application to a target cluster using RHACM. This approach leveraged a Git repository which housed all of the manifests that defined your application. RHACM was able to take those manifests and use them as deployables, which were then deployed to the target cluster.

You also leverage the power of labels and deploy the application to your imported cluster. I highly encourage you to play around with the labels and deploy this application to your local cluster. You can also create other clusters and or applications if you so desire.

<style>
    r {color: Red}
    g {color: Grey}
    </style>
 
<r>   

**If you would like to see how the Ansible Automation Platform “prehook” and “posthook” work inside ACM, you can complete the next section including the ServiceNow section. If you don’t want to experiment or demo this integration, you can skip to the Creating Policies in ACM section.**

</r>