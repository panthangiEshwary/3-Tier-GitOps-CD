
root@ip-172-31-24-103:~/ArgoCd# history
    1  sudo apt update -y
    2  sudo apt update && sudo apt upgrade -y
    3  sudo apt install -y unzip curl
    4  curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    5  unzip awscliv2.zip
    6  sudo ./aws/install
    7  aws --version
    8  aws configure
    9  git clone https://github.com/panthangiEshwary/EKS-Terraform.git
   10  sudo apt update && sudo apt upgrade -y
   11  sudo apt install -y wget unzip
   12  sudo apt update && sudo apt install -y gnupg software-properties-common curl
   13  # Add HashiCorp GPG key
   14  curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
   15  # Add HashiCorp repo
   16  echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
   17  # Update and install Terraform
   18  sudo apt update && sudo apt install -y terraform
   19  # Verify installation
   20  terraform -v
   21  terraform init
   22  ls
   23  cd EKS-Terraform/
   24  terraform init
   25  terraform apply --auto-approve
   26  terraform destroy
   27  ls
   28  nano variables.tf
   29  terraform init
   30  terraform apply --auto-approve
   31  aws eks --region us-east-1 update-kubeconfig --name devopsshack-cluster
   32  sudo apt update
   33  sudo apt install -y apt-transport-https ca-certificates curl
   34  curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/kubernetes-archive-keyring.gpg
   35  echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
   36  sudo apt update
   37  sudo apt install -y kubectl
   38  sudo snap install kubectl --classic
   39  kubectl version --client
   40  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
   41  chmod +x kubectl
   42  sudo mv kubectl /usr/local/bin/
   43  kubectl version --client
   44  kubestl get pods
   45  eksctl utils associate-iam-oidc-provider \ --region us-east-1 \ --cluster devopsshack-cluster \ --approve
   46  curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
   47  sudo mv /tmp/eksctl /usr/local/bin
   48  eksctl version
   49  eksctl utils associate-iam-oidc-provider   --region us-east-1   --cluster devopsshack-cluster   --approve
   50  eksctl create iamserviceaccount   --region us-east-1   --name ebs-csi-controller-sa   --namespace kube-system   --cluster devopsshack-cluster   --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy   --approve   --override-existing-serviceaccounts
   51  kubectl apply -k "github.com/kubernetes-sigs/aws-ebs-csi-driver/deploy/kubernetes/overlays/stable/ecr/?ref=release-1.11"
   52  kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
   53  kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.0/cert-manager.yaml
   54  cd ..
   55  mkdir ArgoCd
   56  cd ArgoCd/
   57  kubectl create ns argocd
   58  kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   59  kubectl get all -n ardocd
   60  kubectl get all -n argocd
   61  kubectl edit svc argocd-serve -n argocd
   62  kubectl edit svc argocd-server -n argocd
   63  kubectl get all -n argocd
   64  kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d; echo
   65  vi sc.yaml
   66  kubectl apply -f sc.yaml
   67  > sc.yaml
   68  nano sc.yaml
   69  kubectl apply -f sc.yaml
   70  cat sc.yaml
   71  >sc.yaml
   72  vi sc.yaml
   73  kubectl apply -f sc.yaml
   74  >sc.yaml
   75  vi sc.yaml
   76  kubectl apply -f sc.yaml
   77  cat sc.yaml
   78  SECRET="devops9848126969"
   79  kubectl -n argocd patch secret argocd-secret   -p '{"stringData":{"webhook.github.secret":"'${SECRET}'"}}'   --type merge
   80  kubectl get ingress -n prod
   81  kubectl get certificate -n prod
   82  kubectl delete certificate eshwary-com-tls -n prod
   83  kubectl get certificate -n prod
   84  history
        
