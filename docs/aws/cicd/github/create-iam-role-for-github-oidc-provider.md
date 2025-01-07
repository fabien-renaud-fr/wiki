# Create IAM role for GitHub OIDC provider

## Create IAM Identity Provider (console)

Follow these instructions to create and manage an IAM OIDC identity provider in the AWS Management Console [1][2].

1. Open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/)
2. In the navigation pane, choose **Identity providers**, and then choose **Add provider**
3. For **Configure provider**, choose **OpenID Connect**
    - **Provider URL**: `https://token.actions.githubusercontent.com`
    - **Audience**: `sts.amazonaws.com`
4. (Optional) For **Add tags**, you can add key–value pairs to help you identify and organize your IdPs
5. When you are done choose **Add provider**

## Create IAM Role for identity federation (console)

1. Open the IAM console at [https://console.aws.amazon.com/iam/](https://console.aws.amazon.com/iam/)
2. In the navigation pane, choose **Roles**, and then choose **Create role** [3]
3. For **Trusted entity type**, choose **Custom trust policy**. The sub format used in this trust policy **sub** claim match GitHub OIDC customization template defined in [Setup GitHub OIDC customization template](./setup-github-oidc-customization-template.md) [4].

```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Principal": {
              "Federated": "arn:aws:iam::<account-id>:oidc-provider/token.actions.githubusercontent.com"
          },
          "Action": "sts:AssumeRoleWithWebIdentity",
          "Condition": {
              "StringEquals": {
                  "token.actions.githubusercontent.com:aud": "sts.amazonaws.com",
                  "token.actions.githubusercontent.com:sub": "repo:<github-organization-name>/<github-repository-name>:ref:refs/heads/master:actor:*"
              }
          }
      }
  ]
}
```

4. Choose **Next**
5. For **Add permissions**, choose policies you want to attach, and then choose **Next**
6. For **Role details**
    - **Role name**: `Terraform<RepositoryName>AssumeRole`
    - **Description**: `N/A`
7. (Optional) For **Add tags**, you can add key–value pairs to help you identify and organize your roles
8. Choose **Create role**

## Create IAM Identity Provider (CLI)

Upcoming soon!

## Create IAM Role for identity federation (CLI)

Upcoming soon!

## References

- [[1] AWS Documentation / AWS Identity and Access Management / Create an OpenID Connect (OIDC) identity provider in IAM / Creating and managing an OIDC provider (console)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_oidc.html#manage-oidc-provider-console)
- [[2] GitHub Docs / Configuring OpenID Connect in Amazon Web Services / Adding the identity provider to AWS](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services#adding-the-identity-provider-to-aws)
- [[3] AWS Documentation / AWS Identity and Access Management / Create a role for a third-party identity provider (federation) / Creating a role for federated users (console)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html#roles-creatingrole-federated-users-console)
- [[4] Wiki / AWS / CICD / GitHub / Setup GitHub OIDC customization template](./setup-github-oidc-customization-template.md)
