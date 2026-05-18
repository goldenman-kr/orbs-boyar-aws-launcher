# Launch Stack Validation Report

- Brand: KRYP Labs
- Repository: `goldenman-kr/orbs-boyar-aws-launcher`
- Template: `cloudformation/template-medium-ami-secrets.yaml`
- Supported region: `us-east-2`
- AMI ID: `ami-06d0ede0e331a96d2`
- AWS resources created during this phase: none
- CloudFormation deployment during this phase: none
- AMI/snapshot changes during this phase: none

## Template delivery strategy

Recommended public-access strategy for this release candidate: GitHub raw URL.

Template raw URL:

```text
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-secrets.yaml
```

Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fraw.githubusercontent.com%2Fgoldenman-kr%2Forbs-boyar-aws-launcher%2Fmain%2Fcloudformation%2Ftemplate-medium-ami-secrets.yaml
```

## Tradeoffs

GitHub raw URL is simple, public, auditable, and convenient for a GitHub Launch Stack button. It is appropriate for the current release-candidate phase.

For a later production or Marketplace release, KRYP Labs should consider version-pinned release assets, a signed artifact workflow, or an AWS-owned S3 publishing flow to improve immutability and availability guarantees.

## Documentation added

- README Launch Stack section
- `docs/QUICK_START.md`
- `docs/SECRETS_MANAGER_SETUP.md`

## Safety scan

Public-doc safety scan passed.

Checks:

- No secrets
- No pre-signed URLs
- No private key exact value
- No AWS credential patterns
- No PEM/key files
- No internal-only local machine paths
- No unapproved 64-hex secret-like literals

## Remaining tasks

- Perform a clean-room Launch Stack test from GitHub after release approval.
- Decide whether to pin Launch Stack links to a Git tag instead of `main`.
- Complete Marketplace source-region and AMI scanning review before Marketplace submission.
