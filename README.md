# OpenShift Virt & Ansible examples

[community.kubevirt](https://github.com/ansible-collections/community.kubevirt) is UNMAINTAINED please use [kubevirt.core](https://github.com/kubevirt/kubevirt.core/tree/main)

* [kubevirt.core.kubevirt_vm](https://kubevirt.io/kubevirt.core/1.1.0/plugins/kubevirt_vm.html)

# Build / Development

## Build Ansible execution environment

```bash

VERSION=$(date +%Y%m%d%H%M)

ansible-builder build \
    --verbosity 3 \
    --container-runtime podman \
    --tag quay.io/openshift-example/kubevirt-ansible-ee:$VERSION

podman push quay.io/openshift-example/kubevirt-ansible-ee:$VERSION
```
