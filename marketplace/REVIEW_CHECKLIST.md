# Marketplace Review Checklist — KRYP Labs Orbs Boyar AWS Launcher

## Current identifiers

- Public AMI: `ami-0111607018603b1cb`
- Region: `us-east-2`
- GitHub repo: https://github.com/goldenman-kr/orbs-boyar-aws-launcher
- Current Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet.yaml
```

## Seller onboarding

- [ ] KRYP Labs AWS Marketplace seller account is approved.
- [ ] Legal entity, tax, banking, and payout details are complete.
- [ ] Seller profile is complete.
- [ ] Support email and support URL are approved.
- [ ] EULA/license approach is selected.
- [ ] Internal owner for Marketplace operations is assigned.

## AMI readiness

- [ ] Confirm Marketplace source-region requirement.
- [ ] Confirm AMI ownership/publishing account alignment.
- [ ] Run AWS Marketplace AMI scan.
- [ ] Confirm no baked secrets, keys, shell history, or prior cloud-init state.
- [ ] Confirm root SSH and authorized key posture.
- [ ] Review OS package patch level.
- [ ] Review root EBS encryption posture.
- [ ] Confirm public AMI ID and backing snapshot are final for submission.

## CloudFormation readiness

- [ ] Validate `cloudformation/template-medium-ami-direct-autonet.yaml`.
- [ ] Validate `cloudformation/template-medium-ami-secrets.yaml`.
- [ ] Decide primary Marketplace template: direct-input or Secrets Manager.
- [ ] Consider making Secrets Manager mode the primary Marketplace path.
- [ ] Confirm `SshAccessCidr=0.0.0.0/0` supports Marketplace SSH validation while `AccessCidr=127.0.0.1/32` keeps node/status endpoints secure-by-default.
- [ ] Confirm Elastic IP auto-create/reuse behavior is acceptable.
- [ ] Confirm stack deletion cleanup behavior.
- [ ] Confirm outputs are reviewer-friendly.

## Product metadata

- [ ] Product title approved.
- [ ] Short description approved.
- [ ] Long description approved.
- [ ] Highlights/features approved.
- [ ] Usage instructions approved.
- [ ] Security notes approved.
- [ ] Support statement approved.
- [ ] Early-access/non-guarantee disclaimer approved.
- [ ] Free pricing strategy approved.

## Screenshots/assets

- [ ] Product logo.
- [ ] Architecture diagram.
- [ ] CloudFormation parameter screenshot.
- [ ] CloudFormation outputs screenshot.
- [ ] Successful Boyar status endpoint screenshot.
- [ ] Successful management status endpoint screenshot.
- [ ] Optional terminal verification screenshot.
- [ ] Public documentation links verified.

## Security and review concerns

- [ ] Document that direct NoEcho input is not a dedicated secret store.
- [ ] Document Secrets Manager recommendation for production.
- [ ] Warn users to restrict `SshAccessCidr` for production and to widen `AccessCidr` only intentionally.
- [ ] Warn users about AWS infrastructure costs.
- [ ] Warn users about retained/reused EIP charges.
- [ ] Clarify that KRYP Labs does not custody private keys.
- [ ] Clarify that validator rewards/economics are not guaranteed.

## Region strategy

- [ ] Decide whether `us-east-2` only is acceptable for first Marketplace review.
- [ ] If more regions are required, plan AMI copy and per-region validation.
- [ ] Update docs/templates if multiple AMI IDs are introduced.

## Blockers before submission

- [ ] Seller onboarding status unknown.
- [ ] Marketplace source-region requirement unknown.
- [ ] Marketplace AMI scan not run.
- [ ] Final support contact details not approved.
- [ ] EULA/license/support URLs not finalized.
- [x] Public SSH validation path remediated with separate `SshAccessCidr`, key-only SSH, and explicit password-auth disablement.
- [ ] Direct-input private key mode needs Marketplace/security review.
- [ ] Root EBS encryption posture needs review.
- [ ] Screenshots/assets not prepared.
- [ ] No Marketplace private test listing has been created.

## Non-actions for this phase

- [x] No Marketplace product submitted.
- [x] No new AMI created.
- [x] No existing AMI/snapshot modified or deleted.
- [x] No additional regions created.
- [x] No AWS resources created.


## Marketplace source-region copy

- Region: `us-east-1`
- AMI ID: `ami-071db7a079c2d5b0c`
- Snapshot ID: `snap-0e5fc4a3dd3019e56`
- Visibility: public
- Purpose: AWS Marketplace source-region registration preparation
