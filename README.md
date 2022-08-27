# terraform-crds-manager

Manage Helm CRDs with Terraform. 

## Why?

Helm is a remarkable piece of technology to manage your Kubernetes deployment, and used along Terraform is perfect for deploying following the GitOps strategy.

However, Helm has a limitation: [it doesn't manage the lifecycle of Custom Resource Definitions][0] (CRDs), meaning it will only install the CRD during the first installation of a chart. Subsequent chart upgrades will not add or remove CRDs, even if the CRDs have changed.

With this module, Terraform can apply the CRDs on its own, managing their lifecycle alongside the Helm chart they belong to.

## Usage

Given a list of links to CRDs definitions, input them to the module. It is strongly suggested to link to versioned CRDs, to ensure the same version of CRDs and charts.

This module uses the [kubectl Terraform provider][2], so you need to configure it, as [illustrated in their guide][3].

### Example

The [kube-promeheus-stack][4] requires eight different CRDs: managing them manually is quite time-consuming, so it is the perfect candidate for this module.

Let's have a `chart_version` variable, where it is specified the version of the chart deployed with the [Helm provider][5].

Alongside, let's specify the CRDs we want to manage:

```terraform
module "kube_prometheus_stack_crds" {
    source = "rpadovani/terraform-crds-manager"
    # version = "0.1.0" # Specify here the latest version of the module

    crds_urls = [
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-alertmanagerconfigs.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-alertmanagers.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-podmonitors.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-probes.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-prometheuses.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-prometheusrules.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-servicemonitors.yaml",
        "https://raw.githubusercontent.com/prometheus-community/helm-charts/kube-prometheus-stack-${var.chart_version}/charts/kube-prometheus-stack/crds/crd-thanosrulers.yaml",
    ]
}
```

There are two important things to notice here:
- we use the raw version of the file: we want to have only the pure YAML manifest to give to the module;
- we specify the version in the URL, so we can be sure the CRDs are kept in sync with the Helm chart we are deploying;


[0]: https://helm.sh/docs/chart_best_practices/custom_resource_definitions/
[2]: https://registry.terraform.io/providers/gavinbunney/kubectl/1.14.0
[3]: https://registry.terraform.io/providers/gavinbunney/kubectl/latest/docs#configuration
[4]: https://github.com/prometheus-community/helm-charts/blob/main/charts/kube-prometheus-stack/README.md
[5]: https://registry.terraform.io/providers/hashicorp/helm/latest/docs
