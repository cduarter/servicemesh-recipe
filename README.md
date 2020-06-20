# RHT Service Mesh Advanced - Homework

Repository with homework sources for RHT Service Mesh Advanced course.

## Download sources

```bash
# Clone Sources
git clone https://github.com/fmenesesg/osm-homework.git

# Go to homework-rhoar
cd osm-homework
```

## Pre Req

(oc - OpenShift Command Line Interface)[https://mirror.openshift.com/pub/openshift-v4/clients/oc/latest/]
(ansible 2.9) [https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html]
Openshift Container Platform 4.2 with Openshift Service Mesh installed -> follow the instrucctions on the fundations (course)[https://learning.redhat.com/mod/scorm/view.php?id=5623]
Install Python dependencies

```bash
pip install requests-oauthlib --user
pip install openshift --user
```

## Demo Application

In order to test Openshift Service Mesh we are going to use the bookinfo app from the istio repository.
To deploy the application follow the following instructions:

```bash
oc new-project bookinfo

oc apply -f https://raw.githubusercontent.com/istio/istio/1.4.0/samples/bookinfo/platform/kube/bookinfo.yaml -n bookinfo

oc expose service productpage
```

## Apply Service Mesh configurations

The goal of the homework is to enable service mesh capabilities on the bookinfo namespace.
The features that need to be enable are:

* The book info namespace should be on the scope of Openshift Service Mesh
* All the deployments should have an envoy proxy
* The trafic between the services should enable mTLS

In order to acomplish previus requirements and this homework principal goal, I have create an ansible playbook that automate all the task requested.


### Ansible Directory Structure

```bash
$ tree .
.
├── homework
│   ├── group_vars
│   │   └── all
│   ├── roles
│   │   └── ServiceMeshMemberRoll
│   │       ├── files
│   │       ├── meta
│   │       │   └── main.yml
│   │       ├── tasks
│   │       │   └── main.yml
│   │       └── templates
│   │           ├── cert.cfg
│   │           ├── destinationrule.yml
│   │           ├── policy.yml
│   │           ├── smmr.yml
│   │           ├── virtualservice.yml
│   │           └── wildcard-gateway.yml
│   └── site.yml
└── README.md

```

* group_vars/all: Global playbook variables

* roles/ServiceMeshMemberRoll/files: Will contain tls.crt and tls.key files

* roles/ServiceMeshMemberRoll/meta/main.yml: Metadata file (author,company, etc)

* roles/ServiceMeshMemberRoll/tasks/main.yml: File that has all the tasks.

* roles/ServiceMeshMemberRoll/templates/*: Template files used by the task file

* site.yml: Main playbook file


## Variables setup

"All" file contains all the variables used by the playbook

```yaml
# API URL of Openshift 4 Instance
openshift_api_url: "https://api.cluster-4313.4313.sandbox1697.opentlc.com:6443"
# Application domain
openshift_apps_domain: "apps.cluster-4313.4313.sandbox1697.opentlc.com"
# Openshift User
openshift_username: admin
# Openshift Password
openshift_password: "r3dh4t1!"
# Project used to deploy Service Mesh Control Plane
control_plane_project_name: "bookretail-istio-system"
# Namespace that will be included into Service Mesh Scope
service_mesh_member_roll_namespaces: "bookinfo"


## Playbook execution

To execute the playbook use the following command

```bash
ansible-playbook site.yml
```
