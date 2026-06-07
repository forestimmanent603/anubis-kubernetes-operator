# Anubis AI Firewall Kubernetes Operator

Kubernetes operator for managing [Anubis](https://github.com/TecharoHQ/anubis) AI Firewall proxies with the `AnubisProxy` custom resource.

## Install

```bash
kubectl apply -f https://github.com/eznix86/anubis-kubernetes-operator/releases/download/v<version>/install.yaml
```

This installs:
- the `AnubisProxy` CRD
- the operator namespace at `anubis-operator-system`
- operator RBAC
- the controller

After that, you create one or more `AnubisProxy` resources for the applications you want to protect.

## Upgrade

Apply the newer release manifest:

```bash
kubectl apply -f https://github.com/eznix86/anubis-kubernetes-operator/releases/download/v<version>/install.yaml
```

This updates the installed operator to the selected release version.

## Uninstall

```bash
kubectl delete -f https://github.com/eznix86/anubis-kubernetes-operator/releases/download/v<version>/install.yaml
```

Uninstalling the operator removes the controller and its resources.

## Usage

After the operator is installed, apply an `AnubisProxy` resource.

For the full values reference used by the operator chart, see [helm-charts/anubis/README.md](helm-charts/anubis/README.md).

Before creating an `AnubisProxy`, create the Anubis signing key Secret:

For example:

```bash
kubectl create secret generic anubis-key \
  --namespace default \
  --from-literal=ED25519_PRIVATE_KEY_HEX="$(openssl rand -hex 32)"
```

Update the namespace and secret name to match your `AnubisProxy` manifest.

To pin a specific Anubis runtime version per `AnubisProxy`, choose a tag from:

- https://github.com/TecharoHQ/anubis/tags

```yaml
apiVersion: anubis.techaro.dev/v1alpha1
kind: AnubisProxy
metadata:
  name: app
  namespace: default
spec:
  target:
    service:
      name: app
      port: 80

  publicURL: https://app.example.com

  anubis:
    image:
      tag: latest
    keys:
      existingSecret: anubis-key

  ingress:
    enabled: true
    className: nginx
    hosts:
      - host: app.example.com
        paths:
          - path: /
            pathType: Prefix
```

1. Install the operator with the release `install.yaml`.
2. Create any prerequisite Secrets or backend Services your proxy needs.
3. Apply an `AnubisProxy` manifest.
4. The operator will do the rest.

See:

- `config/samples/anubis_v1alpha1_anubisproxy.yaml`
- `config/samples/anubis_v1alpha1_anubisproxy_k3s_dev_echo.yaml`

## License

[MIT](LICENSE)
