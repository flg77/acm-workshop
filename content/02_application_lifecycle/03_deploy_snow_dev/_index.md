---
title: "Deploy a Service Now Developer Instance"
date: 2023-10-23T22:07:50+02:00
draft: false
weight: 203
---

## Deploying a ServiceNow Developer Instance

The purpose of this exercise is to take advantage of the Ansible Automation Platform (AAP) you
just deployed to kick off Ansible Jobs (tied to prehook or posthook tasks in the application
lifecycle of ACM.

A **pre hook** will kick off an Ansible job before the application creation / modification.
A **post hook** will kick off an Ansible job after the application creation / modification.

In the following example the Ansible job will trigger the creation of ServiceNow Change
Requests. Perform this step if you would like to show or learn how this integration works. You
will take advantage of this within the application lifecycle engine.

To deploy your own instance of ServiceNow, please visit https://developer.servicenow.com
Select **Sign up and Start Building**

![SNOW Portal](/images/202_17.png)

After signing up and requesting your developer ServiceNow instance (it may look like this
You can click on the third option, Developer Program, and then on Start Building
👇)

![SNOW Welcome Msg](/images/202_18.png)



You can select the Utah version(latest) and soon, you will have an instance URL that looks like
this: https://dev#####.service-now.com
Make note of your instance URL, your admin username and password.
Configure Ansible to trigger a job that will create a ServiceNow
Change Request
Create a ServiceNow Credential Type
In Ansible Automation Platform we need to create a Credential Type for ServiceNow, and then
add the Credentials for our ServiceNow developer instance.


- Select Credential Types, under Administration, in the left menu and then click ADD.
- Name: ServiceNow
- Input Configuration:


```
fields:
- id: SNOW_USERNAME
type: string
label: Service Now Username
- id: SNOW_INSTANCE
type: string
label: Service Now Instance Name (https://devXXXXX.service-now.com)
- id: SNOW_PASSWORD
type: string
label: Service Now Password
secret: true
required:
- SNOW_USERNAME
- SNOW_INSTANCE
- SNOW_PASSWORD
```

- Add the following text to the **INJECTOR CONFIGURATION** field

```
extra_vars:
snow_instance: '{{ SNOW_INSTANCE }}'
snow_password: '{{ SNOW_PASSWORD }}'
snow_username: '{{ SNOW_USERNAME }}'
```

- **SAVE** this new credential type


![SNOW Welcome Msg](/images/202_19.png)

Add the ServiceNow Credentials


Navigate to --> **Resources → Credentials**. 
--> Click on **ADD**.

|**Name:** |ServiceNow Credentials|
|**CREDENTIAL TYPE**|  select **ServiceNow**|
|**ORGANIZATION** | select **Default**|
|**USERNAME**| admin|
|**INSTANCE NAME**| instance name of your developer instance. (https://dev#####.service-now.com)|
|**PASSWORD**| password from your user settings in the ServiceNow dashboard/website.|

--> Then **SAVE**.

![Save AAP Snow creds](/images/202_20.png)

### Create an Ansible Project


In Ansible we need to create a Project

● Select Projects, under Resources, in the left menu. Click on ADD. Enter
snow-create-change-record in the NAME field
● Set the ORGANIZATION field to Default
● Set the SCM TYPE to Git
● Set the SCM URL to: https://github.com/levenhagen/ansible_snow
● Click SAVE


![AAP Projects](/images/202_21.png)


### Create an Ansible Job Template

In Ansible we need to create a Job Template

● Select Templates, under Resources, in the left menu.
● Click on ADD then Add Job Template.
○ NAME: snow-create-change-record
○ INVENTORY field search and select Demo Inventory, and check PROMPT ON
LAUNCH
○ PROJECT: snow-create-change-record
○ PLAYBOOK: select servicenow_changerequest.yml
○ CREDENTIALS: from the dropdown select ServiceNow, and select ServiceNow
Credentials Click SELECT
○ VARIABLES field check PROMPT ON LAUNCH
● Scroll all the way to the bottom, leave everything as default and click SAVE
● Success will indicate it can communicate with ServiceNow and it successfully created a
Change Request in ServiceNow


![AAP Job Template](/images/202_22.png)

To verify all was configured properly click the Launch Button.
Click NEXT, NEXT and LAUNCH.
Verify that the job ran successfully by looking at the output:

![AAP Job Template](/images/202_23.png)

You can also look in your ServiceNow instance.

![Verify in SNOW instance](/images/202_24.png)