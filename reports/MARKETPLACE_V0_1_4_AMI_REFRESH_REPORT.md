# Marketplace v0.1.4 AMI Refresh Report

## Summary

Created a fresh us-east-1 AMI ID for the Marketplace version update that includes the CloudFormation KeyName auto-create behavior.

## New active AMI

- Region: us-east-1
- AMI: `ami-05171a26aaa49fa0d`
- Snapshot: `snap-0e0b5e6379b74d120`
- Public: true
- State: available
- Source AMI: `ami-0a56576d054e9228e`

## Reason

AWS Marketplace version updates commonly require a changed AMI ID. The instance image contents are functionally inherited from the previous validated us-east-1 AMI, while the delivery templates now support optional KeyName with automatic stack-managed EC2 Key Pair creation when omitted.

## Updated delivery templates

All current public templates now reference `ami-05171a26aaa49fa0d`:

- `cloudformation/template-medium-ami-direct-autonet.yaml`
- `cloudformation/template-medium-ami-direct-autonet-github.yaml`
- `cloudformation/template-medium-ami-direct-autonet-github-us-east-1.yaml`

## KeyName behavior

- Existing KeyName selected: reuse existing regional EC2 Key Pair; stack deletion retains it.
- KeyName left empty: stack creates an EC2 Key Pair, applies it to the instance, and deletes it with the stack.
- Outputs expose `AppliedKeyName`, `KeyPairMode`, and auto-created private-key retrieval details when applicable.
