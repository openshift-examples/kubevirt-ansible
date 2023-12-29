---
title: Ansible
linktitle: Ansible
description: OpenShift Virt & Ansible examples
tags: ['ansible','kubevirt','cnv','ocp-v']
---

# OpenShift Virt & Ansible examples

[community.kubevirt](https://github.com/ansible-collections/community.kubevirt) is UNMAINTAINED please use [kubevirt.core](https://github.com/kubevirt/kubevirt.core/tree/main)

* Documentation [kubevirt.core.kubevirt_vm](https://kubevirt.io/kubevirt.core/1.1.0/plugins/kubevirt_vm.html)

# Image Upload example

=== "playbook-upload.yaml"

```yaml
--8<-- "content/kubevirt/ansible/playbook-upload.yaml"
```

=== "Download"

```bash
curl -L -O {{ page.canonical_url }}playbook-upload.yaml
```

# Deploy VM example

=== "playbook-vm.yaml"

```yaml
--8<-- "content/kubevirt/ansible/playbook-vm.yaml"
```

=== "Download"

```bash
curl -L -O {{ page.canonical_url }}playbook-vm.yaml
```


# Build / Development

## Build Ansible execution environment

```bash

VERSION=$(date +%Y%m%d%H%M)

ansible-builder build \
    --verbosity 3 \
    --container-runtime podman \
    --tag quay.io/openshift-examples/kubevirt-ansible-ee:$VERSION

podman push quay.io/openshift-examples/kubevirt-ansible-ee:$VERSION
```
