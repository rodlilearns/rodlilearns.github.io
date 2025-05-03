---
layout: post
title: Red Hat OpenShift for AI Cheatsheet
categories: RHOAI
---
# Red Hat OpenShift for AI Cheatsheet

## User and Resource Mgmt
### Objective
* Manage RHOAI users and control access.
* Manage RHOAI resource allocation.

#### Users

Add users called banana1 and banana2 into a group called data-scientists.
`$ oc adm groups add-users data-scientists banana1 banana2`

Get the odh dashboard configs in YAML.
`$ oc get odhdashboardconfigs odh-dashboard-config -n redhat-ods-applications -o yaml`
OdhDashboardConfig == Custom Resource Definition (CRD) 
groupsConfig controls which groups have which perms. 

Create a project.
`oc create namespace ds-analyse-banana-prices`
or, Create a YAML file called ds-analyse-banana-prices.yaml
```yaml
kind: Namespace
apiVersion: v1
metadata:
  name: ds-analyse-banana-prices
```
`$ oc apply -f ds-analyse-banana-prices.yaml`

A project is a Kubernetes namespace with additional features (RBAC, Integrated Quotas and Limits, Project Templates, Default Resources such as service accounts, image streams, secret/ConfigMaps etc).

#### Resources

Common resources in RHOAI include:
* Data science projects (projects.project.openshift.io)
* Users (users.users.openshift.io)
* Groups (groups.users.openshift.io)
* Role Bindings (rolebindings.rbac.authorization.k8s.io)
* Workbenches (notebooks.kubeflow.org)
* Persistent Volume Claims (persistentvolumeclaim)
* Data Connections (secret)
* Data Science Pipeline Applications (DSPA) (datasciencepipelineapplications.datasciencepipelineapplications.opendatahub.io)
* Inference Services (inferenceservices.serving.kserve.io)
* Model Servers (servingruntimes.serving.kserve.io)

Idle Notebook Culler shuts down pods if a notebook has been inactive for some time.

## Custom Notebook Images
### Objectives: 
* Consistent development env for teams.
* Ensure projects are reproducible between team members.

A Containerfile is a script containing instructions on how to build a container image.
Containerfile example

```bash
FROM domain.com/repo/best-image:1.0

COPY requirements.txt ./

RUN echo "Installing softwares and packages" && \
    pip3 install micropipenv && \
    micropipenv install && \
    rm -f ./requirements.txt
```
FROM specifies the base image.
COPY copies a file into the container image.
RUN executes the provided commands.

Create a container image with a Containerfile in the current directory. Give it a tag "domain.com/repo/best-image:2.0".
`$ podman build . -t domain.com/repo/best-image:2.0`

Push a container image to a container registry for use outside of the local machine.
`$ podman push domain.com/repo/best-image:2.0`

Import custom workbench image into RHOAI

``` plantuml!
As Admin -> Go to Settings -> Notebook Images -> Import a new image
```
Name: Custom Workbench for the Best Data Scientists
Image location: domain.com/repo/best-image:2.0
Description: Images provides the Pandas DataFrames pkg.
Packages:
  Add packages: Pandas
``` plantuml!
As Admin -> Click Import -> As Developer -> Navigate to Data Science Projects -> Select a project -> Navigate to Workbenches -> Create Workbench
```
Name: Custom workbench for Data Science
Image Selection: Custom Workbench for the Best Data Scientists
``` plantuml!
Create Workbench
```
