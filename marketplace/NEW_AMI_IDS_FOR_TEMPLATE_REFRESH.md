# New AMI IDs for Marketplace Template Refresh

Purpose: provide fresh AMI IDs for a new AWS Marketplace version when Marketplace will not refresh the CloudFormation template snapshot without an AMI/version change.

## Source AMIs copied

- us-east-1 source/current: `ami-0a56576d054e9228e`

## New AMIs

- - us-east-1: `ami-0a56576d054e9228e`

## Status

Both AMIs are:

- owned by account `617775257107`
- `available`
- public launch permission enabled
- copied from the previously validated public AMIs
- functionally unchanged; created only to satisfy Marketplace new-version/AMI-ID refresh requirements

## Snapshot IDs

- us-east-2 snapshot: `snap-046c3787978b89a4e`
- us-east-1 snapshot: `snap-0514d553e6359f97e`

## Intended Marketplace usage

For a new AWS Marketplace version, use the new source-region AMI:

```text
us-east-1: ami-0a56576d054e9228e
```

Keep the Marketplace CloudFormation template URL stable:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

That public template already has required `KeyName` and no `VpcId`/`SubnetId` parameters.
