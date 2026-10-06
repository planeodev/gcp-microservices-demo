This directory contains a list of scenarios (different combinations of Kustomize Components) used for testing.
See [/.github/workflows/kustomize-build-ci.yaml](../../.github/workflows/kustomize-build-ci.yaml).

Every scenario (and the base) is also checked by
[/.github/workflows/kustomize-devsecops.yaml](../../.github/workflows/kustomize-devsecops.yaml) —
schema, removed APIs and image availability. `planeo-ghcr-images/` is the base with this fork's own
`ghcr.io/planeodev/gcp-microservices-demo/<service>` images, so their pullability is checked too.
