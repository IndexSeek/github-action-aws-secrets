# GitHub Action AWS Secrets
Demonstration of using GitHub Actions for using AWS Credentials. 

Example for (https://medium.com/dev-genius/secure-your-github-workflows-unlocking-the-power-of-aws-secrets-manager-c5825d8d0da3). 

## AWS Secrets Manager with GitHub Workflows
This repository provides a solution to integrate AWS Secrets Manager with GitHub workflows. By centralizing the secret management approach, you can simplify the management process and enhance your organization's security posture.

## AWS Configuration
### Secret Creation
To create a secret named AWESOME_SECRET with the value Shhh, run:

```bash
aws secretsmanager create-secret --name AWESOME_SECRET --secret-string "Shhh"
```
Make a note of the returned Arn.

### User Creation
To create a user named github-secret-getter, run:

```bash
aws iam create-user --user-name github-secret-getter
```

### Policy Creation
Create a JSON file named awesome_policy.json:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "secretsmanager:GetSecretValue",
            "Resource": "arn:aws:secretsmanager:<AWSRegion>:<AccountId>:secret:<SecretArn>"
        },
        {
            "Effect": "Allow",
            "Action": "secretsmanager:ListSecrets",
            "Resource": "*"
        }
    ]
}
```
Replace the placeholders with appropriate values and then create the policy:

```bash
aws iam create-policy --policy-name awesome_policy --policy-document file://awesome_policy.json
```
Make a note of the returned Arn.

```bash
aws iam attach-user-policy --user-name github-secret-getter --policy-arn <policy-arn>
```

### Create Access Key
To create an access key for the github-secret-getter user, run:

```bash
aws iam create-access-key --user-name github-secret-getter
```
Make a note of the returned AccessKeyId and SecretAccessKey.

## GitHub Configuration
### Configure Action Secrets

Go to Settings > Secrets and variables > Actions.
Click on "New repository secret".
Add the previously created AccessKeyId and SecretAccessKey.
Add a secret for the AWS region.

2. Configure Workflow
Create a file named .github/workflows/main.yml in the repo to test accessing the secret from AWS Secrets Manager.

Teardown (Optional)
To remove the created resources:

```bash
aws iam detach-user-policy --user-name github-secret-getter --policy-arn <your-policy-arn>
aws iam delete-access-key --user-name github-secret-getter --access-key-id <your-access-key>
aws iam delete-user --user-name github-secret-getter
aws secretsmanager delete-secret --secret-id awesome_secret
```

Conclusion
This repository is a hands-on exercise to configure AWS IAM and interact with the AWS CLI. It showcases how to work with GitHub actions while maintaining a limited privileges approach for AWS user permissions.

For a comprehensive explanation, check out the article.

Feel free to fork this repository and try it out! 

🔗 Example Repository

Happy coding! 🚀
