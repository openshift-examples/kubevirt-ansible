---
title: Ansible
linktitle: Ansible
description: OpenShift Virt & Ansible examples
tags: ['ansible','kubevirt','cnv','ocp-v']
icon: material/ansible
---

# Ansible

???+ note

    [community.kubevirt](https://github.com/ansible-collections/community.kubevirt) is UNMAINTAINED please use [kubevirt.core](https://github.com/kubevirt/kubevirt.core/tree/main)

* Documentation [kubevirt.core.kubevirt_vm](https://kubevirt.io/kubevirt.core/1.1.0/plugins/kubevirt_vm.html)

## Playbook examples

??? example "Upload an ISO"

    === "Download: playbook-upload.yaml"

        ```shell
        curl -L -O {{ page.canonical_url }}playbook-upload.yaml
        ```

    === "playbook-upload.yaml"

        ```yaml
        --8<-- "content/kubevirt/ansible/playbook-upload.yaml"
        ```

??? example "Deploy VM example"

    === "Download: playbook-vm.yaml"

        ```shell
        curl -L -O {{ page.canonical_url }}playbook-vm.yaml
        ```

    === "playbook-vm.yaml"

        ```yaml
        --8<-- "content/kubevirt/ansible/playbook-vm.yaml"
        ```

## Ansible execution environment

??? quote "ansible-navigator.yaml"

    ```yaml
    --8<-- "content/kubevirt/ansible/ansible-navigator.yaml"
    ```

??? quote "execution-environment.yml"

    ```yaml
    --8<-- "content/kubevirt/ansible/execution-environment.yml"
    ```

??? quote "ee-requirements.yml"

    ```yaml
    --8<-- "content/kubevirt/ansible/ee-requirements.yml"
    ```

??? quote "ee-python-requirements.txt"

    ```ini
    --8<-- "content/kubevirt/ansible/ee-python-requirements.txt"
    ```

## Run the playbook with ansible-navigator

```shell title="Clone and run"
git clone https://github.com/openshift-examples/kubevirt-ansible.git
cd kubevirt-ansible
cp -v $KUBECONFIG .
export K8S_AUTH_KUBECONFIG=$(basename $KUBECONFIG)
ansible-navigator run playbook-vm.yaml
```

## Run the playbook with Ansible Automation Platform

### Create service account for AAP

```shell
% oc create sa aap
serviceaccount/aap created
oc create token aap
% oc policy add-role-to-user admin -z aap
clusterrole.rbac.authorization.k8s.io/admin added: "aap"
% oc policy add-role-to-user kubevirt.io:admin -z aap
clusterrole.rbac.authorization.k8s.io/kubevirt.io:admin added: "aap"

oc create -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: aap-token
  annotations:
    kubernetes.io/service-account.name: aap
type: kubernetes.io/service-account-token
EOF
```

### Ansible Automatio Plaftorm configuration

* Add credential of the service account to the OpenShift cluster
* Add the git project
* Add execution environment
* Create job


## Build / Development

### Build Ansible execution environment





```shell title="Build and push execution environment"
VERSION=$(date +%Y%m%d%H%M)

ansible-builder build \
    --verbosity 3 \
    --container-runtime podman \
    --tag quay.io/openshift-examples/kubevirt-ansible-ee:$VERSION

podman push quay.io/openshift-examples/kubevirt-ansible-ee:$VERSION
```



