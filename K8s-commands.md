https://aws.amazon.com/blogs/opensource/kubernetes-ingress-aws-alb-ingress-controller/
https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/

eksctl create cluster \
--name 2048 \
--node-type t2.micro \
--nodes 2

wget https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/iam-policy.json

aws iam create-policy \
    --policy-name ALBIngressControllerIAMPolicy \
    --policy-document file://iam-policy.json

Record Policy.Arn of the output for that previous command and use it as $PolicyARN

{
    "Policy": {
        "PolicyName": "ALBIngressControllerIAMPolicy",
        "PolicyId": "ANPAW4YXAIMGO3SMI7IKD",
        "Arn": "arn:aws:iam::474105266956:policy/ALBIngressControllerIAMPolicy",
        "Path": "/",
        "DefaultVersionId": "v1",
        "AttachmentCount": 0,
        "PermissionsBoundaryUsageCount": 0,
        "IsAttachable": true,
        "CreateDate": "2020-10-11T22:40:37+00:00",
        "UpdateDate": "2020-10-11T22:40:37+00:00"
    }
}

eksctl create iamserviceaccount \
       --cluster=2048 \
       --namespace=kube-system \
       --name=alb-ingress-controller \
       --attach-policy-arn="arn:aws:iam::474105266956:policy/ALBIngressControllerIAMPolicy" \
       --override-existing-serviceaccounts \
       --approve


curl -sS "https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.4/docs/examples/alb-ingress-controller.yaml" \
     | sed "s/# - --cluster-name=devCluster/- --cluster-name=2048/g" \
     | kubectl apply -f -

-------------------------------------------------------


curl -sS "https://raw.githubusercontent.com/kubernetes-sigs/aws-alb-ingress-controller/v1.1.8/docs/examples/alb-ingress-controller.yaml" \
     | sed "s/# - --cluster-name=devCluster/- --cluster-name=2048/g" \
     | kubectl apply -f -


openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=chichin.app/O=chichin.app"