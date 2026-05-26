# AWS Marketplace Submission Plan — KRYP Labs Orbs Boyar AWS Launcher

- Brand: KRYP Labs
- Product type: AWS Marketplace AMI-based product
- Intended pricing: free software; customers pay their own AWS infrastructure costs
- Current public AMI: `ami-0111607018603b1cb`
- Current GitHub Launch Stack AMI region: `us-east-2`
- Current CloudFormation template: `cloudformation/template-medium-ami-direct-autonet.yaml`
- GitHub repository: https://github.com/goldenman-kr/orbs-boyar-aws-launcher
- Current Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet.yaml
```

## 1. Current release-candidate structure and public assets

Current public early-access distribution consists of:

- Public sanitized AMI `ami-0111607018603b1cb` in `us-east-2`.
- Public S3-backed direct-input CloudFormation template:
  - `cloudformation/template-medium-ami-direct-autonet.yaml`
  - Public TemplateURL: `https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml`
- Retained Secrets Manager template for higher-security production positioning:
  - `cloudformation/template-medium-ami-secrets.yaml`
- Public GitHub repository with quick start, security, troubleshooting, release notes, and validation reports.
- GitHub early-access tag: `v0.1.0-early-access`.

No additional AMI, region copy, Marketplace submission, or AWS resource creation is part of this preparation phase.

## 2. Required AWS Marketplace seller onboarding steps

Before product submission, KRYP Labs should complete or confirm:

1. AWS Marketplace seller account registration.
2. Seller legal entity, tax, banking, and payout profile.
3. Public seller profile:
   - company name: KRYP Labs
   - support email
   - website / product page
   - privacy policy URL if required
   - EULA or standard contract choice
4. Access for the operator who will create and submit Marketplace AMI products.
5. Internal approval for product naming, support commitments, and public disclaimers.
6. Decision on whether this should be listed as:
   - free AMI product, or
   - paid/professional-support product later.

## 3. AMI-based product requirements

Expected Marketplace AMI product review areas:

- AMI must be owned by the seller account or shared/copied into the correct publishing account.
- Marketplace-supported source region must be confirmed. Current AMI is only in `us-east-2`.
- AMI must pass AWS Marketplace security scanning.
- AMI should not contain secrets, private keys, logs, cloud-init instance history, SSH authorized keys, or user-specific runtime configuration.
- AMI should use supported OS packages and reasonable patch levels.
- Root login and SSH exposure should be reviewed.
- Root volume encryption/default storage posture should be reviewed.
- Product should clearly document required instance type, volume size, networking, and customer-provided parameters.
- Product should define usage instructions that let AWS reviewers launch and validate the instance.

Current known AMI posture:

- Public AMI: `ami-0111607018603b1cb`
- Backing snapshot: `snap-0cce629ae703964d8`
- Sanitized release scan previously passed for no baked validator secrets, no shell history, no cloud-init instance state, and no root authorized keys.
- Runtime configuration is injected at first boot.

## 4. CloudFormation delivery requirements

Marketplace reviewers will need a clear launch path. Current early-access delivery uses a public S3 TemplateURL and GitHub source template.

For Marketplace submission, decide whether to submit:

1. AMI-only product with usage instructions that reference GitHub/CloudFormation, or
2. AMI product with CloudFormation deployment guidance as supporting documentation, or
3. Marketplace-compatible CloudFormation solution if AWS requires/permits it for the chosen listing flow.

CloudFormation template readiness items:

- Template must use the Marketplace-approved AMI ID/region.
- Parameters must be understandable to non-authors.
- Security group defaults must be explicitly disclosed.
- Elastic IP lifecycle must be documented.
- Direct-input private key mode must be positioned carefully because `NoEcho` is not a full secret manager.
- Secrets Manager mode should be the preferred production/security posture.

## 5. Screenshots and assets needed

Prepare the following assets before submission:

- Product logo for KRYP Labs / Orbs Boyar Launcher.
- Architecture diagram showing:
  - AWS Marketplace AMI
  - EC2 instance
  - Elastic IP
  - Security Group
  - EBS root volume
  - Orbs Boyar runtime
  - Ethereum RPC endpoint
  - optional Secrets Manager secret
- AWS Console screenshots:
  - Marketplace product landing page draft
  - CloudFormation Create Stack parameter page
  - Stack outputs showing `ElasticIp`, `BoyarStatusUrl`, `ManagementStatusUrl`
  - Successful Boyar status endpoint
  - Successful management status endpoint
- Terminal screenshots or text snippets for basic verification commands.
- Support/contact image or website link if Marketplace asks for seller assets.

## 6. Support/contact requirements

Marketplace listing should include:

- Support email address under KRYP Labs domain or approved support mailbox.
- Support scope:
  - launcher deployment issues
  - AMI boot/configuration issues
  - documentation defects
- Explicit exclusions:
  - customer AWS account billing
  - customer VPC design
  - Ethereum RPC provider outages
  - validator key custody mistakes
  - Orbs network economics or validator reward guarantees
- Response-time expectation, even for a free listing.
- Security contact for suspected secret exposure or AMI vulnerability reports.

## 7. Likely AWS Marketplace review concerns

Likely review questions/blockers:

- SSH ingress is Marketplace-reviewer reachable by default through `SshAccessCidr=0.0.0.0/0`, while first boot enforces key-only SSH with `PasswordAuthentication no`. Node/status endpoint ingress remains secure-by-default through `AccessCidr=127.0.0.1/32`; users can explicitly choose broader CIDRs.
- Direct private key input: `NoEcho` hides values in CloudFormation UI/API outputs but does not equal Secrets Manager. Marketplace production flow should likely prefer `template-medium-ami-secrets.yaml`.
- Root volume encryption: current template sets `Encrypted: false`; Marketplace/security review may require encryption or reliance on account default EBS encryption.
- Region support: current AMI is `us-east-2` only. Marketplace may require source-region conventions, copies, or broader region support.
- Product support obligations: free AMI still needs a credible support path.
- Runtime dependencies: deployment requires a reachable Ethereum RPC endpoint and Orbs node credentials.
- Endpoint exposure: ports `80` and `7666` are exposed by default for node/status access; document operational risk.
- No TLS/domain/monitoring: acceptable for minimal launcher only if clearly documented.

## 8. Region support strategy

Current strategy:

- Start with `us-east-2` only because the public sanitized AMI exists and has been validated there.

Before Marketplace submission:

1. Confirm AWS Marketplace source-region requirements.
2. Decide whether to keep `us-east-2` only for initial private review or copy to AWS Marketplace's expected source region.
3. If expanding regions, create/copy AMIs only after explicit approval and repeat sanitization/validation per region.
4. Update CloudFormation mappings or docs if multiple AMI IDs are introduced.

## 9. Free pricing strategy

Recommended initial Marketplace pricing:

- Software price: free.
- Customer pays AWS infrastructure charges:
  - EC2 instance, default `r5.large`
  - EBS gp3 root volume, default 256 GiB
  - data transfer
  - Elastic IP charges if applicable
  - Secrets Manager charges if using Secrets Manager mode
  - monitoring/logging if customer enables it

Rationale:

- Lower adoption friction.
- Aligns with open GitHub early-access flow.
- Keeps KRYP Labs support and product validation focused on deployment usability before commercial packaging.

## 10. Direct-input vs Secrets Manager positioning

Recommended positioning:

- Direct-input template (`template-medium-ami-direct-autonet.yaml`): early-access and quick evaluation path.
  - Easier for testers.
  - Uses `PrivateKeyNoLeading0x` with `NoEcho: true`.
  - Must clearly state that `NoEcho` is not a dedicated secret store.
- Secrets Manager template (`template-medium-ami-secrets.yaml`): production/Marketplace security posture.
  - Customer creates AWS Secrets Manager secret.
  - CloudFormation passes only `PrivateKeySecretArn`.
  - EC2 role can read only that specified secret.

For Marketplace submission, prefer Secrets Manager mode in primary usage instructions unless Marketplace reviewer feedback accepts direct-input mode for simplified launch.

## 11. Remaining blockers before Marketplace submission

- AWS Marketplace seller onboarding status unknown.
- Marketplace source-region requirement not confirmed.
- Marketplace AMI scan not run.
- Final product title, support email, EULA/license posture, privacy/support URLs not confirmed.
- SSH reviewer accessibility remediated: `SshAccessCidr` is separate from node/status `AccessCidr`, key-based auth is required, and password auth is explicitly disabled.
- Direct-input private key mode may not be acceptable as primary Marketplace path.
- Root EBS encryption posture needs review.
- No Marketplace-specific screenshots/assets prepared yet.
- No private Marketplace test listing has been created.

## 12. Explicit non-actions in this phase

This preparation does not:

- submit a Marketplace product
- create new AWS resources
- create or copy AMIs
- modify AMI/snapshot visibility
- delete AMIs/snapshots
- add regions


## Marketplace source-region copy

- Region: `us-east-1`
- AMI ID: `ami-071db7a079c2d5b0c`
- Snapshot ID: `snap-0e5fc4a3dd3019e56`
- Visibility: public
- Purpose: AWS Marketplace source-region registration preparation


## Architecture diagram

Use the v0.1.1 AutoNet diagram for Marketplace review. It is generated from `cloudformation/template-medium-ami-direct-autonet.yaml`, uses current official AWS Architecture Icons, and reflects the auto-provisioned VPC, public subnet, Internet Gateway, route table, Security Group, EC2 instance, root EBS block device, Elastic IP handling, Docker Swarm / Boyar runtime, management-service, runtime parameter flow, and public status endpoints.

Public image URL:

```text
https://kryp-labs-marketplace-assets-617775257107-us-east-1.s3.us-east-1.amazonaws.com/marketplace-assets/orbs-boyar-autonet-marketplace-architecture-v0.1.1.png
```
