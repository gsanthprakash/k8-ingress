### Ingress Controller

reference
---
1) https://kubernetes-sigs.github.io/aws-load-balancer-controller/v2.7/

eksctl utils associate-iam-oidc-provider \
 --region us-east-1 \
 --cluster eks-spot-cluster \
 --approve

 # OIDC provider nothing netween authentication beween eks and aws

 2. curl -o iam-policy.json https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.7.0/docs/install/iam_policy.json
 # modified the above file .gov removed.


 3. aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam-policy.json


4. eksctl create iamserviceaccount \
--cluster=eks-spot-cluster \
--namespace=kube-system \
--name=aws-load-balancer-controller \
--attach-policy-arn=arn:aws:iam::905418147390:policy/AWSLoadBalancerControllerIAMPolicy \
--override-existing-serviceaccounts \
--region us-east-1 \
--approve


5. helm repo add eks https://aws.github.io/eks-charts

6. helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=eks-spot-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller




apiVersion: extensions/v1beta1
kind: Ingress
metadata:
    name: echoserver
    namespace: echoserver
    annotations:
        alb.ingress.kubernetes.io/scheme: internet-facing
        alb.ingress.kubernetes.io/target-type: ip
        alb.ingress.kubernetes.io/subnets: subnet-05e1c98ed0f5b109e,subnet-07f5bb81f661df61b,subnet-0a4e6232630820516
        alb.ingress.kubernetes.io/tags: Environment=dev,Team=test
spec:
    rules:
    - host: echoserver.example.com
        http:
        paths: