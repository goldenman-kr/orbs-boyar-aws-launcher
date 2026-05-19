# v0.1.1 AutoNet AMI Version Report

- Brand: KRYP Labs
- Version: v0.1.1 AutoNet
- Source basis: copied from current sanitized public AMIs
- AMI rebuild performed: no
- Marketplace submission attempted: no
- Existing v0.1.0 AMIs/snapshots deleted or modified: no

## New AMIs

### us-east-2 — GitHub Launch Stack / public testing

```text
AMI: ami-0111607018603b1cb
Name: orbs-network-full-node-v0.1.1-autonet-us-east-2
Snapshot: snap-0cce629ae703964d8
Visibility: public
Launch permission: Group=all
Snapshot create volume permission: Group=all
```

### us-east-1 — AWS Marketplace source AMI

```text
AMI: ami-071db7a079c2d5b0c
Name: orbs-network-full-node-v0.1.1-autonet-us-east-1
Snapshot: snap-0e5fc4a3dd3019e56
Visibility: public
Launch permission: Group=all
Snapshot create volume permission: Group=all
```

## Marketplace AMI ID

Use this AMI ID for AWS Marketplace source AMI registration if `us-east-1` is accepted for the listing:

```text
ami-071db7a079c2d5b0c
```

## CloudFormation update

`cloudformation/template-medium-ami-direct-autonet.yaml` now defaults to the v0.1.1 AutoNet public testing AMI in `us-east-2`:

```text
ami-0111607018603b1cb
```

## TemplateURL

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

## Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet.yaml
```

## Verification

- Both AMIs reached `available` state.
- Both AMIs were made public.
- Both AMI launch permissions include `Group=all`.
- Both backing snapshots have public create volume permission `Group=all`.
- Existing v0.1.0 AMIs and snapshots were not deleted.

## Remaining Marketplace work

- Run AWS Marketplace AMI scanning against `ami-071db7a079c2d5b0c`.
- Confirm AWS Marketplace source-region requirements.
- Complete Marketplace seller/product metadata review.
- Submit only after explicit approval.
