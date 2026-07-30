---
title: Ansible
linktitle: Ansible
description: OpenShift Virt & Ansible examples
tags: ['ansible','kubevirt','cnv','ocp-v']
icon: material/ansible
---

# Ansible

This page provides example Ansible playbooks for managing virtual machines on OpenShift Virtualization using the [kubevirt.core](https://github.com/kubevirt/kubevirt.core/tree/main) collection. It covers deploying VMs, uploading ISO images, adjusting VM resources, and running the playbooks with both ansible-navigator and Ansible Automation Platform.

???+ note

    [community.kubevirt](https://github.com/ansible-collections/community.kubevirt) is UNMAINTAINED please use [kubevirt.core](https://github.com/kubevirt/kubevirt.core/tree/main)

* Documentation: [kubevirt.core.kubevirt_vm](https://kubevirt.io/kubevirt.core/1.1.0/plugins/kubevirt_vm.html)

## Playbook examples

???+ example "Deploy VM"

    === "Download: playbook-vm.yaml"

        ```shell
        curl -L -O {{ page.canonical_url }}playbook-vm.yaml
        ```

    === "playbook-vm.yaml"

        ```yaml
        --8<-- "content/kubevirt/ansible/playbook-vm.yaml"
        ```

    <iframe width="560" height="315" src="https://www.youtube.com/embed/y8VpxdkdRkI?si=j_J9sZ1HtMkkyPHn" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

    <iframe width="560" height="315" src="https://www.youtube.com/embed/3moLU6Ueqr0?si=7DR2Nav-_lajotti" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???+ example "Adjust VM resources"

    === "Download: playbook-adjust-vm.yaml"

        ```shell
        curl -L -O {{ page.canonical_url }}playbook-adjust-vm.yaml
        ```

    === "playbook-adjust-vm.yaml"

        ```yaml
        --8<-- "content/kubevirt/ansible/playbook-adjust-vm.yaml"
        ```

    <iframe width="560" height="315" src="https://www.youtube.com/embed/y3sGxfFNdlM?si=nOcxoEI3MDAFjL22" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

???+ example "Upload an ISO"

    === "Download: playbook-upload.yaml"

        ```shell
        curl -L -O {{ page.canonical_url }}playbook-upload.yaml
        ```

    === "playbook-upload.yaml"

        ```yaml
        --8<-- "content/kubevirt/ansible/playbook-upload.yaml"
        ```

## Run the playbook with ansible-navigator

The playbooks run inside an Ansible execution environment container. Copy your kubeconfig into the project directory so it is available inside the container:

```shell title="Clone and run"
git clone https://github.com/openshift-examples/kubevirt-ansible.git
cd kubevirt-ansible
cp -v $KUBECONFIG .
export K8S_AUTH_KUBECONFIG=$(basename $KUBECONFIG)
ansible-navigator run playbook-vm.yaml
```

## Run the playbook with Ansible Automation Platform

The playbooks can also be run from Ansible Automation Platform (AAP). Create a service account on the target OpenShift cluster so AAP can authenticate and manage VMs.

### Create service account for AAP

```shell title="Create service account and token"
% oc create sa aap
serviceaccount/aap created
% oc create token aap
% oc policy add-role-to-user admin -z aap
clusterrole.rbac.authorization.k8s.io/admin added: "aap"
% oc policy add-role-to-user kubevirt.io:admin -z aap
clusterrole.rbac.authorization.k8s.io/kubevirt.io:admin added: "aap"
% oc create -f - <<EOF
apiVersion: v1
kind: Secret
metadata:
  name: aap-token
  annotations:
    kubernetes.io/service-account.name: aap
type: kubernetes.io/service-account-token
EOF
```

### Ansible Automation Platform configuration

Once the service account is created, configure AAP:

1. Add a **Credential** of type *OpenShift or Kubernetes API Bearer Token* using the service account token and the cluster API URL
2. Add a **Project** pointing to the `kubevirt-ansible` git repository
3. Add an **Execution Environment** referencing the custom execution environment image (see [below](#how-to-build-the-execution-environment))
4. Create a **Job Template** that combines the credential, project, and execution environment, then launch it

## Execution environment configuration

The execution environment bundles all required Ansible collections and Python dependencies into a container image so playbooks run consistently across environments.

??? quote "ansible-navigator.yaml"

    ```yaml
    --8<-- "content/kubevirt/ansible/ansible-navigator.yaml"
    ```

??? quote "execution-environment.yml (AAP base image)"

    ```yaml
    --8<-- "content/kubevirt/ansible/execution-environment.yml"
    ```

??? quote "execution-environment-ubi.yml (UBI base image)"

    ```yaml
    --8<-- "content/kubevirt/ansible/execution-environment-ubi.yml"
    ```

## How to build the execution environment

Two execution environment definitions are available: one based on the AAP supported EE image (`execution-environment.yml`) and one based on UBI 9 (`execution-environment-ubi.yml`). The examples below use the UBI variant.

```shell
podman login registry.redhat.io
export VERSION=$(date +%Y%m%d%H%M)
export IMAGE=quay.io/openshift-examples/kubevirt-ansible-ee:${VERSION}
```

### Build on Linux/RHEL

```shell title="Build on Linux/RHEL"
ansible-builder build \
    --verbosity 3 \
    --file execution-environment-ubi.yml \
    --container-runtime podman \
    --tag ${IMAGE}
podman push ${IMAGE}
```

### Multi-arch build on Mac OS

On Mac OS, `ansible-builder build` does not support multi-arch builds directly. Use `ansible-builder create` to generate the build context, then build with podman:

```shell title="Multi-arch build on Mac OS"
ansible-builder create \
    --file execution-environment-ubi.yml \
    --verbosity 3

podman build --platform linux/amd64,linux/arm64 \
   --manifest ${IMAGE} context/

podman manifest push ${IMAGE}
```
