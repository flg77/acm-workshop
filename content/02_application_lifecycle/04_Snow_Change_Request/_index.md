---
title: "Trigger a ServiceNow Change Request via Ansible"
date: 2023-10-23T22:08:23+02:00
draft: false
weight: 204
---

# Create an application to take advantage of the ACM - Ansible integration


The purpose of this short section is to show how ACM integration with Ansible will kick off an
Ansible Job. 

In this case the Ansible Job will run a playbook that will trigger the creation of a
ServiceNow Change Request, exactly like we did and saw in the previous section.

Within ACM navigate to the Applications menu on the left, and click Create application →

Subscription. Enter the following information:

Name: `book-import2`
Namespace: `book-import2`
Under repository types, select the GIT repository
URL: `https://github.com/levenhagen/book-import.git``
Branch: `prehook``
Path: `book-import``
Expand the “Configure automation for prehook and posthook” dropdown menu.
Ansible Automation Platform credential: aapaccess
Select Deploy application resources only on clusters matching specified labels
Label: environment
Value: dev


SAVE the application. Give this a few minutes. The application will complete and in the
application topology view you will see the Ansible prehook, and you can infer that it’s a
ServiceNow change request creation.

![Book Import App](/202_26.png)

Would you like to learn more about the ACM - Ansible integration? Please visit the
official documentation for RHACM located here.

