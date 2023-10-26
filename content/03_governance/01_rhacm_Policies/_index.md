---
title: "RHACM Policies"
date: 2023-10-23T22:11:56+02:00
draft: false
weight: 301
---

# Governance, Risk, and Compliance 
## (Security and compliance use case)

### Creating Policies in ACM


At this point, you have completed the overview labs for Cluster Lifecycle and Application
Lifecycle capabilities in RHACM. In the Cluster Lifecycle Lab, you learned how RHACM can
help manage the lifecycles of your Kubernetes clusters, including both deploying new clusters
and importing existing clusters. In that lab, you configured your RHACM instance to manage an
OpenShift® cluster.

In the Application Lifecycle Lab, you continued exploring RHACM functionality and learned how
to deploy and configure an application. You used the cluster that you added in the first module
as the target for deploying an application.

Now that you have a cluster and a deployed application, you need to make sure that they do not
drift from their original configurations. This kind of drift is a serious problem, because it can
happen from begining and benevolent fixes and changes, as well as malicious activities that you
38might not notice but can cause significant problems. The solution that RHACM provides for this
is the Governance, Risk, and Compliance, or GRC, functionality.
Review GRC Functionality

To begin, it is important to define exactly what GRC is. In RHACM, you build policies that are
applied to managed clusters. These policies can do different things, which are described below,
but they ultimately serve to govern the configurations of your clusters. This governance over
your cluster configurations reduces risk and ensures compliance with standards defined by
stakeholders, which can include security teams and operations teams
This table describes the three types of policy controllers available in RHACM along with the
remediation mode they support:


|Policy Controller|Purpose|Enforce or Inform|
|-----------------|-------|-----------------|
|Configuration|Used to configure any Kubernetes resource across
your clusters. Where these resources are created or
configured is determined by the namespaces you
include (or exclude) in the policy.|Both|
|Certificate|Used to detect certificates that are close to expiring.
You can configure the certificate policy controller by
updating the minimum duration parameter in your
controller policy. When a certificate expires in less
than the minimum duration, the policy becomes
noncompliant. Certificates are identified from secrets
in the included namespaces.|Inform|
|Identity and Access|Management (IAM)The IAM policy controller monitors for the desired
maximum number of users with a particular cluster
role (i.e. ClusterRole) in your cluster.|Inform|
|-------------------|-------------|----------------|


You need to create three different resources in order to implement the policy controllers:
|Resource|Function|
|--------|--------|
|Policy|The Policy defines what you actually want to check and possibly configure (with
enforce). Policies include a policy-template which defines a list of
objectDefinitions. The policy also determines the namespaces it is applied to, as
well as the remediation actions it takes.|
|PlacementRule|Identifies a list of managed clusters that are targeted when using this
PlacementRule.|
|PlacementBinding|Connect the policy to the PlacementRule.|

![Git Hub Policy Sets](/202_27.png)

You can see most of these components running in the hub cluster in the diagram below.
RHACM uses all of these to determine which managed clusters and namespaces the policies
are applied to.

Fortunately, the RHACM console provides an easy way to start creating basic policies, as
shown below. In this example, you can see that when you change values for elements in the
RHACM console, the YAML content is updated.

![Create Policy Sets](/202_28.png)

This is a complex and emerging/evolving topic, and this course is only providing an overview.
Please consult the GRC product documentation for more details on any of these policy
controllers.
We’ll go through a simple example, and create a policy in the default namespace.
For this, we’ll need a little setup:
Navigate to Clusters and access the ClusterSets tab.

![Cluster Sets](/202_29.png)

This is a complex and emerging/evolving topic, and this course is only providing an overview.
Please consult the GRC product documentation for more details on any of these policy
controllers.
We’ll go through a simple example, and create a policy in the default namespace.
For this, we’ll need a little setup:
Navigate to Clusters and access the ClusterSets tab.

![Cluster Sets](/202_30.png)

Click on the 3-dots button on the right, and then on Edit namespaces bindings and add the
default namespace.

![Namespace Bindings](/202_31.png)

Navigate to the Governance screen and click create policy.

![Governance](/202_32.png)

Build a policy with the following information:

- Name: policy-grc-cert
- Namespace: default

Click **NEXT**.

- Policy Templates: Click on Add policy template and select Certificate Management expiration.

Click **NEXT**.

On Placement, click on New placement.
Select global as the clusterSet.

Leave everything else as default and click NEXT.

![Create Policy Review](/202_33.png)

As you can see in the Review page, this policy will be applied to every cluster in the global
ClusterSet, and it will look for expired certificates in them. If there’s a certificate that is set to
expire in less time than the minimumDuration specification, the policy will inform a not
compliant status.
Click SUBMIT.
Please note that initially it will complain that there is an issue with the policy but shortly after
should go green and get a checkmark.





