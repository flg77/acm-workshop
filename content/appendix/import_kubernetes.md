---
title: "Import_kubernetes"
date: 2023-10-24T20:43:49+02:00
draft: true
---

Importing a Kubernetes cluster
You can import previously existing OpenShift clusters, or other supported Kubernetes clusters
for example, IKS, EKS, AKS, or GKE. The following is an example of importing an already
existing OpenShift cluster into ACM.
Click on Add Cluster --> Import Clusters. Under labels make sure you add the
environment=dev label as a label example.
Please note that the name you use for the cluster is not relevant, but it makes sense to
use the actual cluster name in a production environment. Once finished click NEXT, NEXT
and GENERATE CODE.

![Import existing cluster](/appendix_1.png)

From a terminal, login to the target Kubernetes cluster you want to import. Then paste and run
the command you just copied.
You should see this in your terminal window

![Import existing cluster](/appendix_2.png)

Navigate back to ACM and wait for the cluster to become available (should be no more than 5 to
10 minutes).

![Import existing cluster](/appendix_3.png)