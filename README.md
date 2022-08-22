# pyhelm3

Python library for managing Helm releases using Helm 3 (i.e. Tiller-less Helm).

## Installation

`pyhelm3` can be installed directly from GitHub using `pip`:

```sh
pip install git+https://github.com/stackhpc/pyhelm3.git
```

## Usage

```python
from pyhelm3 import Client


# This will use the Kubernetes configuration from the environment
client = Client()
# Or specify the kubeconfig file to use
client = Client(kubeconfig = "/path/to/kubeconfig")


# List the deployed releases
releases = await client.list_releases(all = True, all_namespaces = True)
for release in releases:
    print(release.name, release.namespace)


# Get the current revision for an existing release
revision = await client.get_current_revision("cert-manager", namespace = "cert-manager")
chart_metadata = await revision.chart_metadata()
print(
    revision.release.name,
    revision.release.namespace,
    revision.revision,
    str(revision.status),
    chart_metadata.name,
    chart_metadata.version
)


# Fetch a chart
chart = await client.get_chart(
    "cert-manager",
    repo = "https://charts.jetstack.io",
    version = "v1.8.x"
)
print(chart.metadata.name, chart.metadata.version)
print(await chart.readme())


# Install or upgrade a release
revision = await client.install_or_upgrade_release(
    "cert-manager",
    chart,
    { "installCRDs": True },
    atomic = True,
    wait = True
)
print(
    revision.release.name,
    revision.release.namespace,
    revision.revision,
    str(revision.status)
)
```