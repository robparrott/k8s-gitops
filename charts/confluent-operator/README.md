Note: in order to make this work with GitOps and Flux, you need to comment out each repository references, and let helm implicitly look for the chart in the `charts/` directory.

You also need to upgrade the docker image version of the helm operator to `1.0.0-rc7` or so to avoid a bug.
