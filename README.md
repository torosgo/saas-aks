# Deploy AKS, ACR and a demo app for a multi-tenant environment

A demo project to deploy an AKS cluster, ACR, build and deploy sample Nodejs Express web application for a multi-tenant environment.

## Usage:

### 0- Prerequisites
- Complete [prerequisites](https://learn.microsoft.com/en-us/azure/defender-for-cloud/defender-for-containers-enable) for enabling Microsoft Defender for Containers 
- Complete [prerequisites](https://learn.microsoft.com/en-us/azure/aks/keda-deploy-add-on-arm) for enabling KEDA

### 1- Clone Repository
```bash
git clone <URL of this repo>
```

### 2- Generate Azure deployment credentials and set as GitHub Actions Secret

Create a service principal with the az ad sp create-for-rbac command in the Azure CLI. [More details](https://learn.microsoft.com/en-us/azure/azure-resource-manager/templates/deploy-github-actions?tabs=userlevel#generate-deployment-credentials)

```bash
az ad sp create-for-rbac --name "<sp_name>" --role contributor --scopes /subscriptions/<subscription_id> --sdk-auth
```

Copy the jason output similar to below and paste in GitHub Actions secret with the name AZURE_CREDENTIALS
```bash
 {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

### 3- Make customizations
Make customizations in workflows, ARM templates or app code if necessary.

### 4- Run GitHub  Workflow "Build and Deploy AKS, ACR and application image"
Run this workflow once to provision AKS, ACR, and build/push the application container image.
Tis workflow: 
- Deploys AKS with 
    - Public api server endpoint
    - Enable rbac
    - Enable load balancer "standard" for public services  
    - Enable "azure" cni and network policy
    - Enable container insights
    - Enable Microsoft Defender for Containers.
    - Enable Open Service Mesh 
- Deploys ACR
- Builds and pushes the image to ACR

### 5- Run GitHub Action Workflow "Deploy application to AKS"
Run this workflow as many times as needed whenever an new tenant is onboarded.
This workflow:
- Creates a kubernetes namespace for the tenant
- Applies namespace based resource quota for the tenant
- Applies network policy to isolate namespace from other tenant namespaces
- Pull the application from ACR and deploy to tenant namespace

## Support

No SLA. Continuous development. Use at your own risk. Please read License.

## Contributing

Contribution are welcome.


## Copyright

Copyright &copy; 2023.

## License

This document is open source software licensed under the [Apache License 2.0 license](https://opensource.org/licenses/Apache-2.0).