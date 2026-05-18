# Public AMI Final Release Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Public AMI: `ami-0bfc554348685c913`
- Backing snapshot: `snap-0ecb5473ac7987f1f`
- Result: **Published — AMI is publicly launchable**
- CloudFormation stacks deployed: no
- New AMIs created: no
- AMI/snapshot deleted: no
- Marketplace submission attempted: no

## Summary

The sanitized release AMI is now public and launchable in `us-east-2` for early-access testing through the KRYP Labs Orbs Boyar AWS Launcher GitHub Launch Stack flow.

```text
AMI: ami-0bfc554348685c913
Region: us-east-2
Public: true
State: available
Snapshot: snap-0ecb5473ac7987f1f
```

## Final preflight

Before changing visibility, the following checks passed:

- AMI existed
- AMI state was `available`
- AMI was private before release
- AMI launch permissions were not public before release
- Snapshot existed
- Snapshot state was `completed`
- Snapshot was unencrypted and backed the AMI root volume

## Publication actions

The following visibility permissions were applied:

1. Snapshot `snap-0ecb5473ac7987f1f` received public `createVolumePermission`.
2. AMI `ami-0bfc554348685c913` received public launch permission:

```text
LaunchPermissions: Group=all
```

## Verification after publication

Verified after publication:

- AMI `Public`: `true`
- AMI launch permissions include `Group=all`
- Snapshot create volume permissions include `Group=all`
- AMI state remains `available`
- `r5.large` is offered in `us-east-2`
- Public S3 TemplateURL is readable
- CloudFormation accepts the TemplateURL with `validate-template`
- CloudFormation parameter rendering includes all expected parameters
- Template default `ImageId` is `ami-0bfc554348685c913`

## Public TemplateURL

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

## Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-secrets.yaml
```

## Remaining known limitations

- Early-access testing only; not an AWS Marketplace listing yet.
- Region support is currently `us-east-2` only.
- Users must create their own Secrets Manager secret before launch.
- Users pay their own AWS costs.
- No external-account clean Launch Stack test has been recorded in this report.
- Monitoring, TLS/domain setup, automated backups, and Marketplace packaging are not included.

## Cleanup recommendations

Do not delete older AMIs/snapshots until the public AMI is externally verified and explicit cleanup approval is given.

Candidates for later cleanup after approval:

- Previous test AMI: `ami-06d0ede0e331a96d2`
- Previous test snapshot: `snap-0039ee6a089ecb6a2`
- Intermediate failed sanitized AMI: `ami-016b1be091f5a9278`
- Intermediate failed sanitized snapshot: `snap-0537161ad7b32a47c`

## Marketplace next steps

- Run AWS Marketplace AMI scanning on `ami-0bfc554348685c913`.
- Confirm Marketplace source-region requirements.
- Prepare KRYP Labs seller/listing/legal/support assets.
- Decide versioning/tagging for the public S3 template and GitHub release.
- Run external-account Launch Stack validation before broader announcement.
