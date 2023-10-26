---
title: "Deploy Ansible Automation Platform 2 (AAP2)"
date: 2023-10-23T22:07:25+02:00
draft: false
weight: 202
---
<style>
    r {color: Red}
    g {color: Grey}
    </style>


## Deploying Ansible Automation Platform 2
The purpose of this exercise is to deploy an Ansible Automation Platform and the Automation
Controller (formerly known as Ansible Tower) and integrate it with RHACM to perform pre/post
tasks within the application lifecycle engine. The prehook and posthook task allows you to
trigger an Ansible playbook before and after the application is deployed, respectively. Let’s get
started with installing the Ansible Automation Platform (AAP) operator.

### 1. Install the Operator

![Install Update](/images/202_10.png)

For this exercise, we’ll start by accessing the Operator Hub in the OpenShift
console(local-cluster/ACM Hub Cluster) to deploy AAP. So, for this, go to the Operator Hub
and select the Ansible Automation Platform.


Make sure to select channel stable-2.4-cluster-scoped and click INSTALL.
After a couple of minutes, the APP operator installation will finish. Verify that pods are running
by either accessing Workloads → Pods under the AAP namespace, or by running this
command in the bastion machine:

```
oc get pods -n aap
```

There should be 4 pods in a running state.

## Deploy the Automation Controller

After the operator is successfully deployed, we’ll need to have the Automation Controller
instance. Access the AAP operator dashboard(you can do this by clicking on Installed Operators
and clicking on the AAP Operator), click in the Automation Controller tab. Then click on Create
AutomationController:

![Automation Controller](/images/202_11.png)


Now, a form will be presented to you. Just give your AutomationController instance a name, like
aap2, and hit CREATE. This installation will also take a couple of minutes.
Verify this installation, again, by checking the pods in the aap namespace. Now, there should be
7 pods in a running state.

```
oc get pods -n aap
```

## 3. Access the AAP dashboard

If everything was done correctly, shortly we’ll be able to see an AAP route deployed. Before
accessing the URL, still in the aap namespace, navigate over to Secrets and get the AAP
admin password in the secret <name-of-aap-controller-instance>-admin-password. In this case,
app2-admin-password.

![Automation Controller](/images/202_12.png)

Click on the secret name and you will be able to copy the password at the bottom of the
following page.

Now we’re all set. Access **Networking → Routes**, and click on the AAP route.

**Note:** you may see that **AAP is starting up** and upgrading components, while the **WEB UI**
is setting up. This should just take less than a minute.
Use “admin” as username and the retrieved secret as password to login to AAP.

## 4. Configure AAP
Now that you’re logged in, let’s proceed with the initial configuration.
First of all, when you click on the AAP url, you will be presented with a Subscription dialog
window. Click on username/password and provide your Red Hat credentials(from your account
in access.redhat.com[link], not Red Hat SSO). You’ll need an Ansible subscription in order to be
able to use it. Feel free to use one of your trials and/or the employee SKU.

![Welcome AAP](/images/202_13.png)

Select one and click **NEXT**.

Disable the User Analytics and the Automation Analytics for AAP, we don’t need those for our
example. Click **NEXT**.

![Welcome AAP](/images/202_14.png)

Lastly, accept the end user license agreement by clicking on **SUBMIT**.


## 5. Generate a token for ACM

If everything was done correctly it will redirect to the controller page. In the Ansible dashboard,
generate a token for the admin user. Go to Users, click admin, select TOKENS, then click the
ADD button. Add a short description “Token for use by ACM”, update the SCOPE to Write,
then click **SAVE**.

**ATTENTION!** Save the token value to a text file, you will need this token later.

![Create ACM Token](/images/202_15.png)

![Name ACM Token](/images/202_16.png)

