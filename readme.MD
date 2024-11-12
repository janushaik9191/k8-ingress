# Ingress Controller

```
create iodc provider to connect with our cluster because load balancer is out side of cluster

eksctl utils associate-iam-oidc-provider \
    --region region-code \
    --cluster cluster-name \
    --approve
```
download policies for load balancer controller

curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.10.0/docs/install/iam_policy.json
```
create a policy for AWS loadbalancer controller

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json
```
attach required permissions for a policy created

eksctl create iamserviceaccount \
--cluster=cluster-name \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::AWS-ACCOUNT-ID:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve
```
create a helm repo to install load balancer controller

helm repo add eks https://aws.github.io/eks-charts
```
installing of load balancer controller

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=cluster-name --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
```