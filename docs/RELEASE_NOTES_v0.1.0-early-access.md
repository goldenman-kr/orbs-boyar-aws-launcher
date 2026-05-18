# v0.1.0-early-access - KRYP Labs Orbs Boyar AWS Launcher

Public early-access release for launching an Orbs Boyar validator node on AWS using the KRYP Labs GitHub Launch Stack flow.

## Launch Stack

Launch in `us-east-2`:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Public AMI

```text
Region: us-east-2
AMI ID: ami-0bfc554348685c913
```

Marketplace source-region copy prepared after the early-access release:

```text
Region: us-east-1
AMI ID: ami-0c8d6f2b1c7acdc67
Snapshot: snap-0ed6a777975da2ea5
```

## What is included

- Public sanitized Orbs Boyar Medium AMI in `us-east-2`.
- Direct-input CloudFormation template for early-access testing.
- `PrivateKeyNoLeading0x` as a CloudFormation `NoEcho` parameter.
- Automatic Elastic IP creation and association.
- Optional `ExistingEipAllocationId` reuse for reinstall/recovery workflows.
- Stable stack outputs for:
  - `ElasticIp`
  - `ElasticIpAllocationId`
  - `ElasticIpMode`
  - `BoyarStatusUrl`
  - `ManagementStatusUrl`
  - `SSHCommand`
- Existing Secrets Manager template retained for higher-security production and future Marketplace use.
- Documentation for quick start, security, troubleshooting, Launch Stack flow, EIP automation, and EIP reuse.

## Security notes

The early-access default is optimized for usability:

- The direct template accepts the validator private key in the Create Stack UI as `PrivateKeyNoLeading0x`.
- The parameter is `NoEcho`, so CloudFormation hides it in UI/API outputs.
- `NoEcho` is not a dedicated secret store; the value still passes through CloudFormation parameter handling during stack creation.
- For higher-security production or Marketplace-style deployments, prefer the retained Secrets Manager template.

## Costs

The launcher software is free. Users pay their own AWS infrastructure costs, including EC2, EBS, data transfer, Secrets Manager if used, and Elastic IP charges if applicable.

## Known limitations

- Region support is `us-east-2` only.
- This is an early-access GitHub release, not an AWS Marketplace listing.
- Users must provide their own VPC, public subnet, EC2 Key Pair, Ethereum RPC endpoint, validator node address, and validator private key.
- Default network access exposes ports `22`, `80`, and `7666` to `0.0.0.0/0`; advanced users can restrict `AccessCidr`.
- HTTPS/TLS, domain setup, monitoring dashboards, alerts, backups, and upgrade automation are not included.
- Reused EIPs remain user-managed and may incur charges after stack deletion.
