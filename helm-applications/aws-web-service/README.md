# oam-applications / aws-web-service

Deploying the helm graph [aws-web-service](https://artifacthub.io/packages/helm/aws-web-service/aws-web-service/) with FluxCD on Kubevela. This chart allows you to deploy any webservice on your EKS Cluster to expose it to the internet.

## Infrastructure requirements

- KubeVela
- FluxCD

## Deployment

Before deploying this oam application, you must have the __aws-load-balancer-controller__ and __external-dns__ services deployed and configured on your eks cluster. You can quickly deploy these services as oam applications by following the documentation in the links below.

- [aws-load-balancer-controller oam-application](https://github.com/activa-prefapp/oam-applications/tree/main/applications/aws-load-balancer-controller#readme)
- [external-dns oam-application](https://github.com/activa-prefapp/oam-applications/tree/main/applications/external-dns#readme)

All files are ready to deploy in this repository.

You can also deploy the above services in the traditional way by following the official documentation.

- [aws-load-balancer-controller](https://docs.aws.amazon.com/eks/latest/userguide/network-load-balancing.html)
- [external-dns](https://github.com/kubernetes-sigs/external-dns)

In addition, you will need to have a valid certificate configured for your domain.

After downloading the repository, go to the application directory and modify the necessary values for your deployment like __image__, __host__, __annotations__ (certificate configuration)...  
More information about the values you can change can be found in the [chart documentation](https://artifacthub.io/packages/helm/aws-web-service/aws-web-service/) and at the end of this README.

You can now deploy your web service by running the command:

```
vela up -f aws-web-service.yaml
```

You can check status with the command:

```
vela status aws-web-service -n default  #specify the namespace where it has been deployed
```

Additionally you can check the status of the application with the [VelaUX](https://kubevela.io/docs/installation/standalone#3-install-velaux) dashboard if you have previously installed the addon.

After a few minutes your web service will be online on the specified domain.

## Uninstall

You can undo the changes and remove the application deployment with the following command:

```
vela delete aws-web-service -n default
```

## Configuration / Values

Configure the deployment of the component by adding and modifying the variables in the Kubevela .yaml file before launch. 

For example, to configure the webService.image parameter you must use:

```
        values:
          webService:
            image: ubuntu/nginx:latest
```

This way you can add and/or modify the initial helm chart variables. 


| Name                              | Description                                                                                                                                                                                             | Type    | Default                          |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------|----------------------------------|
| configmap.data                    | Kubernetes common configmap data                                                                                                                                                                        | {}      | {}                               |
| deployment.app                    | App name and container name                                                                                                                                                                             | string  | app                              |
| deployment.replicas               | Container replicas                                                                                                                                                                                      | number  | 1                                |
| deployment.containerPort          | Exposed container port                                                                                                                                                                                  | number  | 80                               |
| deployment.command                | Common unique command                                                                                                                                                                                   | string  | ""                               |
| deployment.entrypointFile         | Command mounted as entrypoint with special values                                                                                                                                                       | string  | ""                               |
| deployment.startupProbe           | Common kubernetes startupProbe                                                                                                                                                                          | {}      | {}                               |
| deployment.livenessProbe          | Common kubernetes livenessProbe                                                                                                                                                                         | {}      | {}                               |
| deployment.readinessProbe         | Common kubernetes readinessProbe                                                                                                                                                                        | {}      | {}                               |
| deployment.volumeMounts           | Common kubernetes volumeMounts                                                                                                                                                                          |         |                                  |
| deployment.imagePullPolicy        | Common kubernetes imagePullPolicy                                                                                                                                                                       | string  | Always                           |
| deployment.nodeSelector.enabled   | Enables nodeSelector feature                                                                                                                                                                            | boolean | false                            |
| deployment.nodeSelector.tags      | Deploys the pods on your tagged node                                                                                                                                                                    | {}      | {}                               |
| deployment.resources.limits.cpu   | Common kubernetes resources limits cpu                                                                                                                                                                  | string  | 100m                             |
| deployment.resources.limits.memory| Common kubernetes resources limits memory                                                                                                                                                               | string  | 128Mi                            |
| deployment.resources.requests.cpu | Common kubernetes resources requests cpu                                                                                                                                                                | string  | 100m                             |
| deployment.resources.requests.memory| Common kubernetes resources requests memory                                                                                                                                                            | string  | 128Mi                            |
| ingress.enabled                   | Enables/Disables Ingress creation                                                                                                                                                                       | boolean | true                             |
| ingress.alb.enabled               | Enables/Disables the ALB                                                                                                                                                                                | boolean | true                             |
| ingress.alb.scheme                | Exposes web service to internet with "internet-facing" or internally with "internal"                                                                                                                    | string  | internet-facing                  |
| ingress.alb.protocol              | Protocol that ALB will use in the ingress, it is not neccesary to set it as "HTTPS", because the chart has an ssl-redirect to 443                                                                       | string  | HTTP                             |
| ingress.alb.listenPorts           | ALB listen ports                                                                                                                                                                                        | string  | '[{"HTTP": 80}, {"HTTPS": 443}]' |
| ingress.alb.groupName             | Name of the group that ingress will belong to. If you have multiple deployments with the chart, it is recommended to use the same group name.                                                           | string  | default-group                    |
| ingress.alb.host                  | Host URL                                                                                                                                                                                                | string  | site.com                         |
| ingress.annotations               | Common aditional annotations for the ingress                                                                                                                                                            | {}      | {}                               |
| ingress.ingressClassName          | Common ingress class name, if you use alb the value should be "alb"                                                                                                                                     | string  | alb                              |
| parameterStore.enabled            | Enables/Disables parameter store                                                                                                                                                                        | boolean | false                            |
| parameterStore.secrets_scm_sa_arn | ARN Role that will use the Service Account to access to get the parameters from the parameter store. This role should be created previosly with Terraform.                                              | string  | ""                               |
| parameterStore.secrets_scm_sa     | Service account name. This service account will use the previous parameter.                                                                                                                             | string  | ""                               |
| parameterStore.splitter           | Parameter Store recomends to use a path when you create a new parameter (/my/parameter/path), but you can generate it with another special character, so if you use another, you should specify it here | string  | "/"                              |
| parameterStore.data               | The data from your parameter store. The key is the env var and the value is the path of your parameter. Example: ```MY_ENV_VAR: /my/parameter/path ```                                                  | {}      | {}                               |
| maintenance_mode                  | If you have problems to deploy your container, this mode will execute ```tail -f /dev/null```                                                                                                           | boolean | false                            |
| service.port                      | Service port and targetPort unified                                                                                                                                                                     | number  | 80                               |
| service.labels                    | Common labels for service                                                                                                                                                                               | {}      | {}                               |
| secrets                           | If you don´t use Parameter Store, you can add your own secrets                                                                                                                                          | {}      | {}                               |
| volume.efs_id                     | For add the `volumeHandle`. Must be the same value as in StorageClass of the pod you want to link to.                                                                                                   | string  | efs-sc                           |
| volume.enabled                    | Enables/Disables the volume (EFS). If the volumes are enabled, the remaining values are required.                                                                                                       | boolean | true                             |
| volume.labels                     | Common labels for pvc                                                                                                                                                                                   | {}      | {}                               |
| volume.mount                      | Mount point for `mountPath`.                                                                                                                                                                            | string  | ""                               |
| volume.runAsGroup                 | The GID to run the entrypoint of the container process. Uses runtime default if unset. If set the value specified in SecurityContext takes precedence.                                                  | string  | ""                               |
| volume.runAsUser                  | The UID to run the entrypoint of the container process. Uses runtime default if unset. If set the value specified in SecurityContext takes precedence.                                                  | string  | ""                               |
| volume.storageSize                | Define the storage capacity in  Ei, Pi, Ti, Gi, Mi, Ki or  E, P, T, G, M, k                                                                                                                             | string  | ""                               |
| webService.image                  | Your Docker Image                                                                                                                                                                                       | string  |   stefanprodan/podinfo           |


