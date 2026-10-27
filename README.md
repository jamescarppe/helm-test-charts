# Helm test charts

Two minimal charts for testing Helm deployments. Both run the official `httpd:2.4` image and serve a page showing the chart, release and namespace.

| Chart | Service type | Purpose |
|---|---|---|
| `apache-basic` | ClusterIP | Confirms a plain Helm install works |
| `apache-loadbalancer` | LoadBalancer | Same app, but requests an external load balancer |

## Publishing as a Helm repository (GitHub Pages)

1. Push this repo to GitHub.
2. Create an empty `gh-pages` branch:
   ```
   git checkout --orphan gh-pages
   git rm -rf .
   git commit --allow-empty -m "init gh-pages"
   git push origin gh-pages
   git checkout master
   ```
3. In **Settings → Pages**, set the source to the `gh-pages` branch.
4. Push any change under `charts/` to `master`. The workflow packages each chart, creates a GitHub Release per chart version, and writes `index.yaml` to `gh-pages`.

Bump `version` in a chart's `Chart.yaml` whenever you change it, or the release step will skip it.

## Installing

```
helm repo add helm-test https://<github-user>.github.io/<repo-name>
helm repo update
helm install web-basic helm-test/apache-basic -n helm-test --create-namespace
helm install web-lb helm-test/apache-loadbalancer -n helm-test
```

Or straight from a clone, no repo needed:

```
helm install web-basic ./charts/apache-basic -n helm-test --create-namespace
```

## Checking the result

```
kubectl get pods,svc -n helm-test
```

For `apache-loadbalancer`, an `EXTERNAL-IP` of `<pending>` means the cluster has no load balancer provider (bare metal without MetalLB, kind without cloud-provider-kind, etc.). The pod still runs; only the external address is missing.

## Removing

```
helm uninstall web-basic web-lb -n helm-test
```
