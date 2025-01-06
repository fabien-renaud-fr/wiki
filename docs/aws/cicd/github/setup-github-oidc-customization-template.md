# Setup GitHub OIDC customization template

This page explains how to setup a custom OpenID Connect (OIDC) template hosted on GitHub to authenticate with AWS using
GitHub as an identity provider.

GitHub Actions can authenticate with AWS services using OIDC. This eliminates the need for long-lived credentials by
dynamically requesting temporary credentials during workflow execution. By default, tokens generated use
`repo:<organization>/<repository>:ref:refs/heads/<branch>` sub format, but changes to
`repo:<organization>/<repository>:env:<environment>` when specifying workflow's environment [1].

Following GitHub workflow customize the format of OIDC sub to
`repo:<organization>/<repository>:ref:refs/heads/<branch>:actor:*` on selected repositories. This format enhance
security by including both the `branch` and `actor` in the `sub` claim. In AWS, you can adjust the IAM role's trust
policy to allow role assumption only for tokens with specific branch(es) and actor(s) [2].

## Create a new GitHub Apps

1. Go to your organization's `Settings` / `Developper settings` / `GitHub Apps` page and click on `New GitHub App`
    - GitHub App name: ` frd-github-organization-app`
    - Description: `GitHub App that perform organization related actions in GitHub Actions workflows`
    - Homepage URL: `https://github.com/orgs/fabien-renaud-fr`
    - Webhook: Uncheck `Active`
    - Permissions:
        - Repository permissions:
            - Actions: `Read and write`
            - Metadata: `Read only`
        - Organization permissions:
            - Administration: `Read and write`
    - Where can this GitHub App be installed: `Only this account`
    - Click on `Create GitHub App`
2. On `General` / `Private keys`, click on `Generate a private key`
3. On `Install App`, click on `Install`
    - Select `All repositories`, or manually select repositories that you want to customize
    - Click on `Install`

## Create a new GitHub repository

- Create a new GitHub repository
- Go to your repository's `Settings` / `Secrets and variables` / `Actions`
    - Click on `New repository secret`:
        - Name: `CI_GITHUB_APPS_<GITHUB_APP_NAME>_APP_ID`
        - Secret: `<github-app-private-key>`
    - Click on `Variables` / `New repository variable`:
        - Name: `CI_SECRET_GITHUB_APPS_<GITHUB_APP_NAME>_PRIVATE_KEY`
        - Value: `<github-app-id>`
- Create `.github/workflows/99-set-oidc-customization-template.yml` file:

```yaml
name: 99. 🔒 Set OIDC customization template
on:
  workflow_dispatch:
    inputs:
      repository-name:
        description: '(Required) The repository name'
        required: true
        type: string
permissions:
  id-token: write # This is required for requesting the JWT
jobs:
  set-oidc-customization-template:
    name: 🔒 Set OIDC customization template
    runs-on: ubuntu-22.04
    steps:
      - name: 🔓 Generate token for fabien-renaud-fr GitHub Organization Agent
        id: generate-token
        uses: tibdex/github-app-token@v1
        with:
          app_id: ${{ vars.CI_GITHUB_APPS_FRD_GITHUB_ORGANIZATION_APP_APP_ID }}
          private_key: ${{ secrets.CI_SECRET_GITHUB_APPS_FRD_GITHUB_ORGANIZATION_APP_PRIVATE_KEY }}

      - name: 🌍 Set organization-level OIDC customization template
        uses: octokit/request-action@v2.x
        env:
          GITHUB_TOKEN: ${{ steps.generate-token.outputs.token }}
        with:
          route: PUT /orgs/{org}/actions/oidc/customization/sub
          org: fabien-renaud-fr
          # https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/about-security-hardening-with-openid-connect#configuring-the-oidc-trust-with-the-cloud
          # environment: The name of the environment used by the job. To include the environment claim you must reference an environment
          # ref: The git ref that triggered the workflow run
          include_claim_keys: '["repository", "ref", "actor"]' # Actor is added for audit log in CloudTrail, but is not verified in authentication process

      - name: 🚩 Set repository's OIDC customization template
        uses: octokit/request-action@v2.x
        env:
          GITHUB_TOKEN: ${{ steps.generate-token.outputs.token }}
        with:
          route: PUT /repos/{owner}/{repo}/actions/oidc/customization/sub
          owner: fabien-renaud-fr
          repo: ${{ inputs.repository-name }}
          # https://github.com/aws-actions/configure-aws-credentials/issues/454#issuecomment-1299519020
          # use_default: true means it will not use the organization's customized template.
          use_default: false
```

## References

- [[1] GitHub Docs / Configuring OpenID Connect in Amazon Web Services / Configuring the role and trust policy](https://docs.github.com/en/actions/security-for-github-actions/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services#configuring-the-role-and-trust-policy)
- [[2] Wiki / AWS / CICD / GitHub / Create IAM role for GitHub OIDC provider](./create-iam-role-for-github-oidc-provider.md)
