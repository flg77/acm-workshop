---
title: "Create a new cluster"
date: 2023-10-23T22:01:13+02:00
draft: true
weight: 101
---

# Cluster Lifecycle (Cluster Lifecycle Management use case)

#### At a high level Cluster Lifecycle management is about creating, upgrading, and destroying and importing clusters in a multi cloud environment.

In the 2nd email you received from the RHDP system, it included AWS credentials, Access Key ID, Secret Access Key, and the Base DNS Domain.  In order to create a new OpenShift cluster in the AWS cloud we will need these keys to create a Provider Connection. On the left bar, select Credentials and then select Add Credential.

You will need to provide connection details

![No Credentials](/101_1.png)

**Credential Type:** Choose Amazon Web Services and then, Amazon Web Services again

**Credential Name:**  aws

**Namespace:** open-cluster-management

**Base DNS Domain:** This is in the email from the RHDP system

Click **NEXT**

**Access Key ID:** 

**Secret Access Key ID:** 

Click **NEXT** - We don’t need to configure a Proxy

**Red Hat OpenShift pull secret:**  Get from your Red Hat login

**SSH private and public keys:**  Use an existing key pair or generate a new one (see link below)

Click **NEXT**

**Verify the information** and click **ADD**

Please refer to Creating a cloud connection for Amazon Web Services for more information on how to complete the step.

![New Credentials AWS](/101_2.png)

### Create a new OpenShift cluster in AWS

From the Clusters page, select Create Cluster
Select Amazon Web services and then Standalone.
Select the Infrastructure provider credential: aws (may be already selected)

```Name: cluster1```

Leave the Cluster set empty for now

Select a Release Image, choose a `4.13 version

Add a label of `environment=prod.`

Click **NEXT**

Change the region to *see table below*

![Node Pool Region](/101_3.png)

Depending on the Availability Zone you provisioned your ACM Hub Cluster choose the following regions:

|Availability Zone (AZ) | Region |
|-------------|--------|
|NORTH AMERICA|Select us-west-1 or us-west-2|
|EUROPE / EMEA|Select eu-west-2 or eu-west-3|
|ASIA PACIFIC| Select ap-southeast-2 or ap-northeast-2 or ap-east-1|


Please note that the deployment might still fail, if so please retry at different availability
zone, we have very limited amount of elastic IP addresses in these accounts.

Leave everything else as is(default) and then click NEXT on the other screens or select 7 -
Review and create on the menu and then click CREATE

![Create Cluster](/101_4.png)

In about 45 minutes this new cluster will be ready to go!

![Provisioning Finished](/101_5.png)

If you choose an earlier version of OpenShift, you will also have the option to upgrade
OpenShift if you like. This will also take some time.