# Anubis Chart Values

Reference for the values consumed by the Anubis operator chart.

For operator installation and a full `AnubisProxy` example, see the top-level [README](../../README.md).

For upstream Anubis runtime behavior and configuration details, see:

- https://github.com/TecharoHQ/anubis/tags
- https://anubis.techaro.lol/docs/admin/installation#configuration

## Values

For upstream runtime configuration details behind values such as `anubis.envExtra`, `anubis.policyFile`, and `publicURL`, see:

- https://anubis.techaro.lol/docs/admin/installation#configuration

| Key | Default | Description |
| --- | --- | --- |
| `nameOverride` | `""` | Override the chart name portion used in generated resource names. |
| `fullnameOverride` | `""` | Override the full generated resource name. |
| `target.service.name` | `""` | Name of the backend Kubernetes Service Anubis should protect. |
| `target.service.port` | `80` | Port of the backend Kubernetes Service Anubis should protect. |
| `publicURL` | `""` | Canonical external URL Anubis should use for redirects and generated links. Maps to upstream `PUBLIC_URL`. |
| `anubis.image.repository` | `ghcr.io/techarohq/anubis` | Anubis runtime image repository. |
| `anubis.image.tag` | `latest` | Anubis runtime image tag. |
| `anubis.image.pullPolicy` | `IfNotPresent` | Image pull policy for the Anubis container. |
| `anubis.replicas` | `1` | Number of Anubis replicas to run. |
| `anubis.port` | `8923` | Container port exposed by the Anubis HTTP server. |
| `anubis.metrics.enabled` | `true` | Enable the Anubis metrics endpoint in the pod. |
| `anubis.metrics.port` | `9090` | Metrics port exposed by the Anubis container. |
| `anubis.metrics.service.enabled` | `false` | Create a dedicated Service for metrics scraping. |
| `anubis.metrics.service.type` | `ClusterIP` | Service type for the dedicated metrics Service. |
| `anubis.metrics.service.port` | `9090` | Service port for the dedicated metrics Service. |
| `anubis.metrics.service.annotations` | `{}` | Extra annotations for the dedicated metrics Service. |
| `anubis.metrics.service.labels` | `{}` | Extra labels for the dedicated metrics Service. |
| `anubis.targetOverride` | `""` | Override the computed `TARGET` URL when Anubis should proxy somewhere custom. |
| `anubis.policyFile` | `/etc/anubis/botPolicies.yaml` | Mount path used for inline or external policy configuration. |
| `anubis.envExtra` | `[{name: DIFFICULTY, ...}]` | Extra environment variables passed directly to the Anubis container. |
| `anubis.keys.existingSecret` | `""` | Existing Secret name containing `ED25519_PRIVATE_KEY_HEX`. |
| `anubis.config` | `""` | Inline `botPolicies.yaml` content. Generates a ConfigMap when set. |
| `anubis.existingConfigMap` | `""` | Existing ConfigMap name providing `botPolicies.yaml`. |
| `anubis.resources` | requests/limits block | Container resource requests and limits for Anubis. |
| `anubis.securityContext.runAsUser` | `1000` | Pod-level user ID for the Anubis pod. |
| `anubis.securityContext.runAsGroup` | `1000` | Pod-level group ID for the Anubis pod. |
| `anubis.securityContext.runAsNonRoot` | `true` | Require the Anubis pod to run as a non-root user. |
| `anubis.securityContext.seccompProfile.type` | `RuntimeDefault` | Pod-level seccomp profile for the Anubis pod. |
| `anubis.containerSecurityContext.allowPrivilegeEscalation` | `false` | Disable privilege escalation for the Anubis container. |
| `anubis.containerSecurityContext.capabilities.drop` | `[ALL]` | Linux capabilities dropped from the Anubis container. |
| `service.type` | `ClusterIP` | Service type for the main Anubis HTTP Service. |
| `service.port` | `80` | Service port for the main Anubis HTTP Service. |
| `networkPolicy.enabled` | `false` | Create a NetworkPolicy for Anubis pods. HTTP stays open by default. |
| `networkPolicy.metrics.from` | `[]` | NetworkPolicy peers allowed to reach the metrics port. |
| `ingress.enabled` | `false` | Create an Ingress pointing to the main Anubis Service. |
| `ingress.className` | `""` | Ingress class name for the generated Ingress. |
| `ingress.annotations` | `{}` | Extra annotations for the generated Ingress. |
| `ingress.hosts` | `[{host: "", paths: [{path: "/", pathType: Prefix}]}]` | Host and path rules for the generated Ingress. |
| `ingress.tls` | `[]` | TLS configuration for the generated Ingress. |
| `gateway.enabled` | `false` | Create an `HTTPRoute` pointing to the main Anubis Service. |
| `gateway.parentRefs` | `nil` | Gateway API parent references for the generated `HTTPRoute`. |
| `gateway.hostnames` | `nil` | Gateway API hostnames for the generated `HTTPRoute`. |

## Examples

### Secret

Create the signing key Secret referenced by `anubis.keys.existingSecret`:

```bash
kubectl create secret generic anubis-key \
  --namespace default \
  --from-literal=ED25519_PRIVATE_KEY_HEX="$(openssl rand -hex 32)"
```

### Inline policy file

Use `anubis.config` to generate the policy ConfigMap from your `AnubisProxy` values:

```yaml
anubis:
  keys:
    existingSecret: anubis-key
  config: |
    rules:
      - path: /login
        rateLimit: 5
      - path: /api/v1/internal
        action: block
```

### Extra environment variables

Use `anubis.envExtra` for upstream Anubis settings that are not exposed as first-class values:

```yaml
anubis:
  envExtra:
    - name: REDIRECT_DOMAINS
      value: app.example.com
    - name: SLOG_LEVEL
      value: INFO
```

### Runtime version override

Use `anubis.image.tag` to pin a specific upstream Anubis version for one `AnubisProxy`.
Available tags are published at:

- https://github.com/TecharoHQ/anubis/tags

```yaml
anubis:
  image:
    tag: latest
```
