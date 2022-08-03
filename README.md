# Introduction

Use Github Actions to automatically build your react-app and upload the build to S3.

# Prerequisite

- Github as an identity provider in AWS. If not configured, add token.actions.githubusercontent.com as provider and sts.amazonaws.com as provider.

- An IAM role with S3 Access. The trust relation of the role should contain github actions as principle otherwise it will deny.

# Details

AWS provides the aws-iam-authenticator as an action to login into AWS. It supports different login mechanisms. However, Open ID connect it suited best for deployments as we don't need to hardcore secret key and access id into repository secrets and the generated token is also short lived.

The file is in `.github/workflows/deploy-to-s3.yaml`.

# File Samples

> Role Trust Relationships

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::<ACCOUNT_ID>:oidc-provider/token.actions.githubusercontent.com"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "token.actions.githubusercontent.com:aud": "sts.amazonaws.com"
        }
      }
    },
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::<ACCOUNT_ID>:user/<user_name>"
      },
      "Action": ["sts:AssumeRole", "sts:TagSession"],
      "Condition": {}
    }
  ]
}
```

# More Info

https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect
https://stackoverflow.com/questions/69883862/not-authorized-to-perform-ststagsession-on-resource
https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-policies-s3.html
