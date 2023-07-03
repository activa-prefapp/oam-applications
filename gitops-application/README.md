## oam-applications / gitops

GitOps is a continuous delivery method that allows developers to automatically deploy applications by changing code and declarative configurations in a Git repository, with "git-centric" operations such as PRs and commits.

To set up a GitOps process, such as the one in this repository, in KubeVela you must use the [FluxCD plugin](https://kubevela.io/docs/end-user/gitops/fluxcd).

GitOps will automatically synchronize the configuration in the repository with the cluster. First, we need a repository that stores all the configuration files you need: such as some native Kubernetes Deployment resources, Secret, ConfigMap, etc. In addition, you can store the KubeVela application in the repository.

The directory structure of the test repository is as follows:

```
oam-gitops-sample/
├── .github/
│   └── workflows/
├── app/
│   ├── Dockerfile
│   └── index.html
├── infrastructure/
│   ├── deployment-app.yaml
│   ├── ingres.yaml
│   └── service.yaml
```

The application configured here will deploy all the components located in the infrastructure/ path and update changes as they occur on the kubernetes cluster.

## Infrastructure requirements

- KubeVela
- FluxCD

## Requirements for the deployment of the sample application

In order to deploy the configured application it must meet the requirements described in the [oam-gitops-sample](https://github.com/activa-prefapp/oam-gitops-sample) repository .

## Deployment of the GitOps application on KubeVela

Once all the requirements defined above have been met you can deploy a GitOps application with FluxCD on KubeVela according to [official documentation](https://kubevela.io/docs/end-user/gitops/fluxcd#preparing-the-configuration-repository). 

To perform the deployment download the [oam-applications](https://github.com/activa-prefapp/oam-applications) repository, go to the specified directory and deploy the application with the command:

```
vela up -f infra-gitops-app.yaml
```

You can check the status with the command

```
candle status infra-gitops -n gitops
```

Additionally you can check the status of the application with the [VelaUX](https://kubevela.io/docs/installation/standalone#3-install-velaux) dashboard if you have previously installed the addon.

## Uninstall

You can undo the changes and remove the application deployment with the following command:

```
vela delete infra-gitops -n gitops
```

## Other examples

Other OAM GitOps applications, which have been used throughout the project, have been added to the other-examples folder.