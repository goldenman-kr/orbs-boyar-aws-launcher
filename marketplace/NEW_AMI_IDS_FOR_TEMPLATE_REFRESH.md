# New AMI ID for Marketplace Template Refresh

Purpose: provide fresh AMI IDs for a new AWS Marketplace version when Marketplace will not refresh the CloudFormation template snapshot without an AMI/version change.

## Source AMI copied

- us-east-1 previous source: `ami-0a56576d054e9228e`

## New AMI

- us-east-1: `ami-05171a26aaa49fa0d`

## Status

The new AMI is:

- owned by account `617775257107`
- `available`
- public launch permission enabled
- copied from the previously validated public AMI
- functionally unchanged; created only to satisfy Marketplace new-version/AMI-ID refresh requirements

## Snapshot IDs

- us-east-1 snapshot: `snap-0e0b5e6379b74d120`

## Intended Marketplace usage

For a new AWS Marketplace version, use the new source-region AMI:

```text
us-east-1: ami-05171a26aaa49fa0d
```

Keep the Marketplace CloudFormation template URL stable:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

That public template supports optional `KeyName` with auto-create fallback and has no `VpcId`/`SubnetId` parameters.
