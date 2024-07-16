# devspec-registry-tools

<div id="header">

[![Apache2.0 License](https://img.shields.io/badge/license-Apache2.0-brightgreen.svg)](LICENSE)
</div>

Provide support for devspec registries

Issue tracking repo: https://github.com/devspec/api with label area/registry

## Build

### Prerequisite

The current release relies on [oapi-codegen 1.12.4](https://github.com/deepmap/oapi-codegen/tree/v1.12.4) for OpenAPI source generation. See the [Index Server README](index/server/README.md#source-generation) for more information.

To install:
`go install github.com/deepmap/oapi-codegen/cmd/oapi-codegen@v1.12.4`

### Instructions
If you want to run build scripts with Podman, set environment variable
`export USE_PODMAN=true`

To build all components together (recommended) for dev/test, run `bash ./build_registry.sh` to build a Devspec Registry index image populated with mock devspec registry data under `tests/registry/`.

Once built, push to a container registry:

```
docker tag devspec-index <registry>/<username>/devspec-index:latest
docker push <registry>/<username>/devspec-index:latest
```

See the following for component specific build:

- [Building the OCI Registry](oci-registry/README.md#build)
- [Building the Index Server](index/server/README.md#build)

## Deploy

### Via the Devspec Registry Operator

We recommend using the [Devspec Registry Operator](https://github.com/devspec/registry-operator) to install a Devspec Registry on your Kubernetes or OpenShift cluster. Consult [its Readme](https://github.com/devspec/registry-operator#running-in-cluster).

### Via Helm Chart

A Helm chart is provided if you don't wish to use an operator. To install (Helm 3):

```bash
$ helm install devspec-registry ./deploy/chart/devspec-registry \ 
    --set global.ingress.domain=<ingress-domain> \
	--set devspecIndex.image=<index-image> \
	--set devspecIndex.tag=<index-image-tag>
```

Where `<ingress-domain>` is your cluster ingress domain, `<index-image>` is the devspec index image, and `<index-image-tag>` is the corresponding tag.

Example for custom devspec registry on Minikube:

```bash
$ helm install devspec-registry ./deploy/chart/devspec-registry \
    --set global.ingress.domain="$(minikube ip).nip.io" \
	--set devspecIndex.image=quay.io/someuser/devspec-index \
	--set devspecIndex.tag=latest
```

Deploy with custom registry viewer:

```bash
$ helm install devspec-registry ./deploy/chart/devspec-registry \
    --set global.ingress.domain="$(minikube ip).nip.io" \
	--set devspecIndex.image=quay.io/someuser/devspec-index \
	--set devspecIndex.tag=latest \
	--set registryViewer.image=quay.io/someuser/registry-viewer \
	--set registryViewer.tag=latest
```

Deploy *headless* (without registry viewer):

```bash
$ helm install devspec-registry ./deploy/chart/devspec-registry \
    --set global.ingress.domain="$(minikube ip).nip.io" \
	--set global.headless=true \
	--set devspecIndex.image=quay.io/someuser/devspec-index \
	--set devspecIndex.tag=latest
```

For more information on the Helm chart, consult [its readme](deploy/chart/devspec-registry/README.md).

## Contributing

Please see our [contributing.md](./CONTRIBUTING.md).
