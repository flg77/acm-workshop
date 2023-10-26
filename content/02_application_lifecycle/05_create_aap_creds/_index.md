---
title: "Create Ansible Automation Platform Credentials"
date: 2023-10-23T22:10:27+02:00
draft: false
weight: 205
---

### Create AAP credential in ACM


Here, we are going to set up the credential which is going to allow ACM to interact with our AAP
instance. Click on Credentials on the left menu and select Add Credential button.

Credential type: Ansible Automation Platform
Credential name: aapaccess
Namespace: open-cluster-management

Click NEXT

For Ansible Tower Host enter you Ansible instance URL, you will find this information on
previous screen if not check the ROUTES on your HUB Cluster
For Ansible Tower token enter the admin user token you generated earlier
In order to save, click NEXT and ADD. You’ll be redirected to the Credentials page.

![AAP Creads ACM](/images/202_24.png)