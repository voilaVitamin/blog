---
draft: false 
date: 2024-08-30
tags:
  - Kubernetes
  - FluentBit
  - Cloudwatch
---

# Setting up FluentBit for a k8s cluster and sending logs to Cloudwatch logs

This blog defines the step I took to send container logs from a kubernetes cluster to cloudwatch.

<!-- more -->

Prerequisites:
- Kind 
- Kubectl
- Helm 
- Terraform

## Create user for use with fluentbit

1. Create Iam policy 
```

# Define the IAM user
resource "aws_iam_user" "cloudwatch_user" {
  name = "cloudwatch-user"
}

resource "aws_iam_policy" "cloudwatch_policy" {
  name        = "CloudWatchFullAccess"
  description = "Policy to allow full access to CloudWatch"
  
  policy = jsonencode({
    Version = "2012-10-17"
    Statement = [
      {
        Effect   = "Allow"
        Action   = "cloudwatch:*"
        Resource = "*"
      }
    ]
  })
}

# Attach the policy to the IAM user
resource "aws_iam_user_policy_attachment" "user_policy_attachment" {
  user       = aws_iam_user.cloudwatch_user.name
  policy_arn = aws_iam_policy.cloudwatch_policy.arn
}
```

2. Create the access key and secret access key using the following command
```
aws iam create-access-key --user-name cloudwatch-user
```

3. Store the credentials in a `.env` file in the following format
```
AWS_ACCESS_KEY_ID=<acccesskey>
AWS_SECRET_ACCESS_KEY=<secretaccesskey>
```

## Setting up fluentbit
1. Create a kind cluster
```
kind create cluster --name fluent
```

2. Make sure the kind cluster has been configured in the kubeconfig, when using kubectl 
```
kubectl config current-context
```

3. Add the helm repo and create the release for helm operator
```
helm repo add fluent https://fluent.github.io/helm-charts
helm install fluent-operator fluent/fluent-operator
```

4. Create the credential secret in the amazon-cloudwatch namespace using the following command
```
kubectl create secret generic cloudwatch-user -n amazon-cloudwatch --from-env-file=path/to/.env
```


