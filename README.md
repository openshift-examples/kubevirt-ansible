# OpenShift Virt & Ansible examples




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
