# Ingress Controller

## OIDC Provider

REGION-CODE=us-east-1
CLUSTER-NAME=roboshop-dev
ACC-ID=759713897143
eksctl utils associate-iam-oidc-provider \
    --region $REGION-CODE \
    --cluster $CLUSTER-NAME \
    --approve

Download IAM Policy

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.13.4/docs/install/iam_policy_us-gov.json


Create IAM Policy

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
    

Create IAM role and K8 service. Mapping between IAM Role and Kubernetes service account

eksctl create iamserviceaccount \
--cluster=$CLUSTER_NAME \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::<AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region= \
--approve

# Install Load Balancer controller drivers

````
helm repo add eks https://aws.github.io/eks-charts

````
````
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=$CLUSTER_NAME --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
````