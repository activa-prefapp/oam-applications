# oam-applications / aws-load-balancer-controller


Deploying the helm graph [aws-load-balancer-controller](https://artifacthub.io/packages/helm/aws/aws-load-balancer-controller) with FluxCD on Kubevela.

## Introduction
AWS Load Balancer controller manages the following AWS resources
- Application Load Balancers to satisfy Kubernetes ingress objects
- Network Load Balancers to satisfy Kubernetes service objects of type LoadBalancer with appropriate annotations

You may need to create an IAM OIDC provider and create the necessary policies for a correct deployment of the chart. To set up all the requirements you can visit the chart website [here](https://artifacthub.io/packages/helm/aws/aws-load-balancer-controller#prerequisites). You can also visit the requirements section in the [organisation's documentation](https://github.com/activa-prefapp/documentation/blob/main/README.md).

## Infrastructure requirements

- KubeVela
- FluxCD
- Helm

## Deployment

After downloading the repository go to the application directory and change the value of ``clusterName`` to the name of your cluster in the .yaml files of the deployment.

Then run the command:

For install the AWS Load Balancer controller, if using iamserviceaccount

```
vela up -f iam-aws-load-balancer.yaml -n kube-system
```
For install the AWS Load Balancer controller, if not using iamserviceaccount

```
vela up -f noiam-aws-load-balancer.yaml -n kube-system
```

You can check status with the command:

```
vela status aws-load-balancer-controller -n kube-system
```

Additionally you can check the status of the application with the [VelaUX](https://kubevela.io/docs/installation/standalone#3-install-velaux) dashboard if you have previously installed the addon.

You can view the ALB logs with the command:

```
kubectl logs -f -n kube-system -l app.kubernetes.io/name=aws-load-balancer-controller
```

## Uninstall

You can undo the changes and remove the application deployment with the following command:

```
vela delete aws-load-balancer-controller -n kube-system
```

## Configuration / Values

Configure the deployment of the component by adding and modifying the variables in the Kubevela .yaml file before launch. 

For example, to configure the linux.enabled parameter you must use:

```
        values:
          clusterName: "activa-prefapp"
```

This way you can add and/or modify the initial helm chart variables. 

| Parameter                                      | Description                                                                                              | Default                                                                            |
|------------------------------------------------| -------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| `image.repository`                             | image repository                                                                                         | `public.ecr.aws/eks/aws-load-balancer-controller` |
| `image.tag`                                    | image tag                                                                                                | `<VERSION>`                                                                        |
| `image.pullPolicy`                             | image pull policy                                                                                        | `IfNotPresent`                                                                     |
| `clusterName`                                  | Kubernetes cluster name                                                                                  | None                                                                               |
| `cluster.dnsDomain`                            | DNS domain of the Kubernetes cluster, included in TLS certificate requests                               | `cluster.local`                                                                    |
| `securityContext`                              | Set to security context for pod                                                                          | `{}`                                                                               |
| `resources`                                    | Controller pod resource requests & limits                                                                | `{}`                                                                               |
| `priorityClassName`                            | Controller pod priority class                                                                            | system-cluster-critical                                                            |
| `nodeSelector`                                 | Node labels for controller pod assignment                                                                | `{}`                                                                               |
| `tolerations`                                  | Controller pod toleration for taints                                                                     | `{}`                                                                               |
| `affinity`                                     | Affinity for pod assignment                                                                              | `{}`                                                                               |
| `configureDefaultAffinity`                     | Configure soft pod anti-affinity if custom affinity is not configured                                    | `true`                                                                             |
| `topologySpreadConstraints`                    | Topology Spread Constraints for pod assignment                                                           | `{}`                                                                               |
| `deploymentAnnotations`                        | Annotations to add to deployment                                                                         | `{}`                                                                               |
| `podAnnotations`                               | Annotations to add to each pod                                                                           | `{}`                                                                               |
| `podLabels`                                    | Labels to add to each pod                                                                                | `{}`                                                                               |
| `additionalLabels`                             | Labels to add to all components                                                                          | `{}`                                                                               |
| `rbac.create`                                  | if `true`, create and use RBAC resources                                                                 | `true`                                                                             |
| `serviceAccount.annotations`                   | optional annotations to add to service account                                                           | None                                                                               |
| `serviceAccount.automountServiceAccountToken`  | Automount API credentials for a Service Account                                                          | `true`                                                                             |
| `serviceAccount.imagePullSecrets`              | List of image pull secrets to add to the Service Account                                                 | `[]`                                                                               |
| `serviceAccount.create`                        | If `true`, create a new service account                                                                  | `true`                                                                             |
| `serviceAccount.name`                          | Service account to be used                                                                               | None                                                                               |
| `terminationGracePeriodSeconds`                | Time period for controller pod to do a graceful shutdown                                                 | 10                                                                                 |
| `ingressClass`                                 | The ingress class to satisfy                                                                             | alb                                                                                |
| `createIngressClassResource`                   | Create ingressClass resource                                                                             | true                                                                               |
| `ingressClassParams.name`                      | IngressClassParams resource's name, default to the aws load balancer controller's name                   | None                                                                               |
| `ingressClassParams.create`                    | If `true`, create a new ingressClassParams                                                               | true                                                                               |
| `ingressClassParams.spec`                      | IngressClassParams defined ingress specifications                                                        | {}                                                                                 |
| `region`                                       | The AWS region for the kubernetes cluster                                                                | None                                                                               |
| `vpcId`                                        | The VPC ID for the Kubernetes cluster                                                                    | None                                                                               |
| `awsApiEndpoints`                              | Custom AWS API Endpoints                                                                                 | None                                                                               |
| `awsApiThrottle`                               | Custom AWS API throttle settings                                                                         | None                                                                               |
| `awsMaxRetries`                                | Maximum retries for AWS APIs                                                                             | None                                                                               |
| `enablePodReadinessGateInject`                 | If enabled, targetHealth readiness gate will get injected to the pod spec for the matching endpoint pods | None                                                                               |
| `enableShield`                                 | Enable Shield addon for ALB                                                                              | None                                                                               |
| `enableWaf`                                    | Enable WAF addon for ALB                                                                                 | None                                                                               |
| `enableWafv2`                                  | Enable WAF V2 addon for ALB                                                                              | None                                                                               |
| `ingressMaxConcurrentReconciles`               | Maximum number of concurrently running reconcile loops for ingress                                       | None                                                                               |
| `logLevel`                                     | Set the controller log level - info, debug                                                               | None                                                                               |
| `metricsBindAddr`                              | The address the metric endpoint binds to                                                                 | ""                                                                                 |
| `webhookBindPort`                              | The TCP port the Webhook server binds to                                                                 | None                                                                               |
| `webhookTLS.caCert`                            | TLS CA certificate for webhook (auto-generated if not provided)                                          | ""                                                                                 |
| `webhookTLS.cert`                              | TLS certificate for webhook (auto-generated if not provided)                                             | ""                                                                                 |
| `webhookTLS.key`                               | TLS private key for webhook (auto-generated if not provided)                                             | ""                                                                                 |
| `webhookNamespaceSelectors`                    | Namespace selectors for the wekbook | None
| `keepTLSSecret`                                | Reuse existing TLS Secret during chart upgrade                                                           | `true`                                                                             |
| `serviceAnnotations`                           | Annotations to be added to the provisioned webhook service resource                                      | `{}`                                                                               |
| `serviceMaxConcurrentReconciles`               | Maximum number of concurrently running reconcile loops for service                                       | None                                                                               |
| `targetgroupbindingMaxConcurrentReconciles`    | Maximum number of concurrently running reconcile loops for targetGroupBinding                            | None                                                                               |
| `targetgroupbindingMaxExponentialBackoffDelay` | Maximum duration of exponential backoff for targetGroupBinding reconcile failures                        | None                                                                               |
| `syncPeriod`                                   | Period at which the controller forces the repopulation of its local object stores                        | None                                                                               |
| `watchNamespace`                               | Namespace the controller watches for updates to Kubernetes objects, If empty, all namespaces are watched | None                                                                               |
| `disableIngressClassAnnotation`                | Disables the usage of kubernetes.io/ingress.class annotation                                             | None                                                                               |
| `disableIngressGroupNameAnnotation`            | Disables the usage of alb.ingress.kubernetes.io/group.name annotation                                    | None                                                                               |
| `defaultSSLPolicy`                             | Specifies the default SSL policy to use for HTTPS or TLS listeners                                       | None                                                                               |
| `externalManagedTags`                          | Specifies the list of tag keys on AWS resources that are managed externally                              | `[]`                                                                               |
| `livenessProbe`                                | Liveness probe settings for the controller                                                               | (see `values.yaml`)                                                                |
| `env`                                          | Environment variables to set for aws-load-balancer-controller pod                                        | None                                                                               |
| `hostNetwork`                                  | If `true`, use hostNetwork                                                                               | `false`                                                                            |
| `dnsPolicy`                                    | Set dnsPolicy if required                                                                                | `ClusterFirst`                                                                     |
| `extraVolumeMounts`                            | Extra volume mounts for the pod                                                                          | `[]`                                                                               |
| `extraVolumes`                                 | Extra volumes for the pod                                                                                | `[]`                                                                               |
| `defaultTags`                                  | Default tags to apply to all AWS resources managed by this controller                                    | `{}`                                                                               |
| `replicaCount`                                 | Number of controller pods to run, only one will be active due to leader election                         | `2`                                                                                |
| `podDisruptionBudget`                          | Limit the disruption for controller pods. Require at least 2 controller replicas and 3 worker nodes      | `{}`                                                                               |
| `updateStrategy`                               | Defines the update strategy for the deployment                                                           | `{}`                                                                               |
| `enableCertManager`                            | If enabled, cert-manager issues the webhook certificates instead of the helm template, requires cert-manager and it's CRDs to be installed | `false`                                          |
| `enableEndpointSlices`                         | If enabled, controller uses k8s EndpointSlices instead of Endpoints for IP targets                       | `false`                                                                            |
| `enableBackendSecurityGroup`                   | If enabled, controller uses shared security group for backend traffic                                    | `true`                                                                             |
| `backendSecurityGroup`                         | Backend security group to use instead of auto created one if the feature is enabled                      | ``                                                                                 |
| `disableRestrictedSecurityGroupRules`          | If disabled, controller will not specify port range restriction in the backend security group rules      | `false`                                                                            |
| `objectSelector.matchExpressions`              | Webhook configuration to select specific pods by specifying the expression to be matched                 | None                                                                               |
| `objectSelector.matchLabels`                   | Webhook configuration to select specific pods by specifying the key value label pair to be matched       | None                                                                               |
| `serviceMonitor.enabled`                       | Specifies whether a service monitor should be created, requires the ServiceMonitor CRD to be installed   | `false`                                                                            |
| `serviceMonitor.additionalLabels`              | Labels to add to the service account                                                                     | `{}`                                                                               |
| `serviceMonitor.interval`                      | Prometheus scrape interval                                                                               | `1m`                                                                               |
| `serviceMonitor.namespace`                     | Namespace in which Prometheus is running                                                                 | None                                                                               |
| `clusterSecretsPermissions.allowAllSecrets`    | If `true`, controller has access to all secrets in the cluster.                                          | `false`                                                                            |
| `controllerConfig.featureGates`                | set of `key: value` pairs that describe AWS load balance controller features                             | `{}`                                                                               |
| `ingressClassConfig.default`                   | If `true`, the ingressclass will be the default class of the cluster.                                    | `false`                                                                            |

