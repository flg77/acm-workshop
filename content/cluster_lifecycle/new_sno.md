---
title: "New Single Node"
date: 2023-10-23T22:01:20+02:00
draft: false
weight: 2
---
# Creating a Single Node Cluster (SNO) in AWS 

In this exercise we will show you how to create a single node cluster (OCP 4.13 or above required) in order to save some time and resources when building clusters for testing.

** Please NOTE that provisioning SNO clusters in public clouds is not currently supported, we only support SNO clusters as bare metal, we leverage the public cloud in the example below to showcase the functionality only. **

From the *Clusters* page select Create Cluster

Select Amazon Web services and then Standalone.

Select the Infrastructure provider credential: `aws`
For *name*: `cluster2-sno` or add the desired cluster name. Leave the ClusterSet empty for now 
Select a Release Image, choose a OCP 4.13 version
Add a label of `environment=qa`. Click *NEXT*
Change the region to *see table below* 


|Availability Zone (AZ) | Region |
|-------------|--------|
|NORTH AMERICA|Select us-west-1 or us-west-2|
|EUROPE / EMEA|Select eu-west-2 or eu-west-3|
|ASIA PACIFIC| Select ap-southeast-2 or ap-northeast-2 or ap-east-1|


Please note that the deployment might still fail, if so please retry at different availability zones, we have a very limited amount of elastic IP addresses in these accounts.

Click on Worker Pool 1 and change the Node count to 0

![Node Pool 0](/101_6.png)


Proceed with everything else as is and while at final step, before proceeding turn YAML: ON

![Review config](/101_7.png)

Click on “install-config” in the YAML window pane and change the master replica number to 1 (will likely be 3).  Double check that the worker replica is 0.

![Check yaml](/101_8.png)

![SNO creation](/101_9.png)



