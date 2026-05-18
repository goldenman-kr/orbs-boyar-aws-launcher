# Changelog

## v0.1.0-early-access - 2026-05-18

Public early-access release for KRYP Labs Orbs Boyar AWS Launcher.

Included:

- Public sanitized AMI `ami-0bfc554348685c913` in `us-east-2`.
- Public S3-backed Launch Stack flow using `cloudformation/template-medium-ami-direct.yaml`.
- Direct private key input through `PrivateKeyNoLeading0x` with `NoEcho: true`.
- Automatic Elastic IP creation and association.
- Optional `ExistingEipAllocationId` reuse for reinstall/recovery.
- Public stack outputs for Elastic IP, status URLs, and SSH command.
- Retained Secrets Manager template for higher-security production and future Marketplace use.

Not included:

- AWS Marketplace submission.
- New AMI creation.
- AMI/snapshot modification or cleanup.

## 0.1.0-rc.1 - 2026-05-18

Initial release-candidate package for KRYP Labs Orbs Boyar AWS Launcher.

Included:

- Secrets Manager hardened Medium AMI CloudFormation template
- Public README
- Security notes
- Troubleshooting guide
- Marketplace planning notes
- Architecture summary
- Public-safe `.env.template` example

Validation basis:

- Medium AMI direct validation passed
- Medium AMI CloudFormation launch validation passed
- Secrets Manager CloudFormation launch validation passed

Not included:

- AWS Marketplace submission
- AMI copy/share
- New AMI creation
- Deployment logs or temporary test reports
