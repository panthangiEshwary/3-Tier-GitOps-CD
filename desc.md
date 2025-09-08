**Proof of Concept: Fully Automated GitOps CI/CD Pipeline on AWS EKS**

**1. Abstract**
This document serves as a Proof of Concept (POC) for a fully automated, end-to-end GitOps CI/CD pipeline on AWS. The solution successfully demonstrates the integration of Terraform for infrastructure provisioning and Argo CD for continuous deployment. The core principle of this architecture is to treat Git as the single source of truth for all application and infrastructure configurations. This approach ensures that any changes to the Git repository are automatically and securely synchronized with the Amazon Elastic Kubernetes Service (EKS) cluster, providing a robust, scalable, and auditable deployment workflow.

**2. Solution Architecture**
The implemented pipeline operates on a declarative GitOps model. The architecture consists of the following key components:

Git Repository: Serves as the central control plane, storing application manifests (Kubernetes YAML files) and infrastructure-as-code (Terraform files).

CI Pipeline: A separate process (not detailed in this document) that builds and tags Docker images based on source code changes.

AWS EKS: The managed Kubernetes service where the containerized application is deployed and runs.

Argo CD: A GitOps operator that continuously monitors the Git repository. When it detects a change, it automatically applies the updated manifests to the EKS cluster, ensuring the live state matches the desired state in Git.

Load Balancer: Manages and distributes incoming traffic to the application's services, exposing them to the internet.

3. Implementation and Workflow
The implementation was performed in a structured, phased approach to ensure each component was correctly deployed and validated.

3.1 Infrastructure Provisioning with Terraform
The entire cloud infrastructure, including the Virtual Private Cloud (VPC), public subnets, and the EKS cluster itself, was provisioned programmatically using Terraform.

Tooling Setup: The necessary command-line tools—AWS CLI, Terraform, kubectl, and eksctl—were installed and configured for local management of AWS and Kubernetes resources.

Cluster Provisioning: The Terraform configuration was applied to create all required AWS resources.

git clone [https://github.com/panthangiEshwary/EKS-Terraform.git](https://github.com/panthangiEshwary/EKS-Terraform.git)
cd EKS-Terraform/
terraform init
terraform apply --auto-approve

Validation: The Terraform command successfully created the EKS cluster and its dependencies.

Kubeconfig Setup: The local kubectl context was configured to connect to the newly provisioned EKS cluster.

aws eks --region us-east-1 update-kubeconfig --name devopsshack-cluster

This command downloads the cluster's credentials, allowing kubectl to manage resources.

3.2 Core Cluster Component Deployment
With the cluster active, essential services were deployed to enable advanced functionality like dynamic storage and external access.

IAM OIDC Provider: An IAM OIDC provider was associated with the EKS cluster to enable Kubernetes service accounts to securely assume AWS IAM roles. This is a critical prerequisite for the EBS CSI driver.

eksctl utils associate-iam-oidc-provider \
  --region us-east-1 \
  --cluster devopsshack-cluster \
  --approve

EBS CSI Driver: The AWS EBS CSI driver was installed to allow Kubernetes to provision and manage AWS Elastic Block Store volumes dynamically for stateful applications.

# Create the IAM Service Account for the EBS CSI driver
eksctl create iamserviceaccount \
  --region us-east-1 \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster devopsshack-cluster \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --override-existing-serviceaccounts

# Apply the EBS CSI driver manifests
kubectl apply -k "[https://github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11](https://github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11)"

Validation: The IAM service account was successfully created via CloudFormation.

Ingress Controller and Cert-Manager: The Nginx Ingress Controller was deployed to provide a single entry point for all incoming traffic, and Cert-Manager was deployed to automate the issuance of TLS certificates for HTTPS.

kubectl apply -f [https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml](https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml)
kubectl apply -f [https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml](https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml)

Validation: All manifests were applied without errors.

EBS StorageClass: A StorageClass was created to define a storage class that uses the EBS CSI provisioner. This allows applications to request persistent storage without needing to manually create EBS volumes.

apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: ebs-sc
provisioner: ebs.csi.aws.com
volumeBindingMode: WaitForFirstConsumer
reclaimPolicy: Retain
parameters:
  type: gp3

Validation: The StorageClass was successfully created.

3.3 Argo CD Setup and Git Integration
Argo CD was installed and configured to automate application deployments directly from the Git repository, establishing the GitOps workflow.

Argo CD Installation: The core Argo CD components were deployed into their own dedicated namespace.

kubectl create ns argocd
kubectl apply -n argocd -f [https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml](https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml)

Service Exposure: The Argo CD API server was exposed using an AWS LoadBalancer to allow external access to the UI.

# Change the service type to LoadBalancer
kubectl edit svc argocd-server -n argocd

Initial Password Retrieval: The initial admin password was retrieved from a Kubernetes secret.

kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo

Validation: The command printed the decoded password.

Git Repository Connection: The application's Git repository was successfully connected to Argo CD.
Validation: The repository connection status was "Successful" in the Argo CD UI.

GitHub Webhook: A webhook was configured in the GitHub repository to trigger an immediate sync in Argo CD upon every git push event.

SECRET="devops9848126969"
kubectl -n argocd patch secret argocd-secret \
  -p '{"stringData":{"webhook.github.secret":"'${SECRET}'"}}' \
  --type merge

Validation: The webhook was successfully created with the correct payload URL and a shared secret.

4. Final Validation
Argo CD Application Status: The Argo CD dashboard showed the application and its resources (pods, services) in a "Synced" state, confirming that the cluster state matched the Git repository. The backend deployment was in a "Progressing" state as it rolled out new pods, indicating that Argo CD was actively managing the deployment.

Public Application Access: The application's login page was accessible via the Ingress LoadBalancer URL. This final step confirmed that all networking components were correctly configured and the application was successfully deployed and exposed.

5. Conclusion
This POC successfully demonstrates a fully automated, end-to-end GitOps CI/CD pipeline. By adopting a declarative, code-driven approach, the solution ensures a consistent, auditable, and reliable deployment process. This model is a significant improvement over traditional CI/CD methods, enabling rapid and safe application updates, and provides a strong foundation for future deployments.
