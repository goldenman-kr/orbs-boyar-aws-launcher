# Quick Start

This guide launches the KRYP Labs Orbs Boyar AWS Launcher release candidate with the public S3-backed direct-input Launch Stack flow.

## 1. Prepare the private key value

For early-access testing, the default Launch Stack flow accepts the validator private key directly as `PrivateKeyNoLeading0x`.

Accepted format:

```text
<64-hex-private-key-without-0x>
```

The CloudFormation parameter is marked `NoEcho`, so the value is hidden in the Create Stack UI and API outputs. However, it still exists temporarily in CloudFormation parameter handling during stack creation. Do not paste real private keys into issue trackers, screenshots, logs, shell history, or chat.

For higher-security production use, use the separate Secrets Manager template instead.

## 2. Click Launch Stack

Launch in `us-east-2`:

[![Launch Stack](https://img.shields.io/badge/Launch%20Stack-us--east--2-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml)

If you prefer to inspect the exact CloudFormation template before launching, open the public S3 template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct.yaml
```

## 3. Fill stack parameters

Required parameters:

- `VpcId` — VPC in `us-east-2`
- `SubnetId` — public subnet in `us-east-2`
- `AccessCidr` — CIDR allowed to access SSH (`22`) and node/status endpoints (`80`, `7666`); default `0.0.0.0/0`
- `ExistingEipAllocationId` — optional; leave empty for a new auto-created EIP, or provide an existing AllocationId to preserve the node IP during reinstall/recovery
- `KeyName` — EC2 Key Pair in `us-east-2`
- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — validator node address, 40 hex characters, no `0x`
- `PrivateKeyNoLeading0x` — direct NoEcho private key parameter, 64 hex characters with no `0x`

Defaults:

- `ImageId`: `ami-0bfc554348685c913` (public AMI in `us-east-2`)
- `InstanceType`: `r5.large`
- `VolumeSize`: `256`

Acknowledge `CAPABILITY_IAM` because the stack creates an EC2 IAM role and instance profile.

Default network access exposes SSH and node/status endpoints publicly (`0.0.0.0/0`). This is intentional for the launcher default and public node accessibility. You are responsible for securing your EC2 key pair and AWS account. Advanced users can restrict `AccessCidr` to a narrower CIDR if desired.

## 4. Record the Elastic IP

By default, the stack automatically creates and associates an Elastic IP. Record the `ElasticIp` and `ElasticIpAllocationId` stack outputs; the status URLs and SSH command use this address. If `ExistingEipAllocationId` is empty, CloudFormation releases the auto-created EIP when the stack is deleted. Advanced users can provide an existing AllocationId to preserve the same node IP across reinstall/recovery; reused EIPs are not released by this stack and may incur charges.

## 5. Verify endpoints

After stack status is `CREATE_COMPLETE`, check stack outputs for:

- `ElasticIp`
- `ElasticIpAllocationId`
- `ElasticIpMode`
- `BoyarStatusUrl`
- `ManagementStatusUrl`
- `SSHCommand`

Expected:

```text
/services/boyar/status HTTP 200
:7666/status HTTP 200
```

Optional SSH verification:

```bash
sudo systemctl status boyar.service --no-pager --full
sudo docker service ls
sudo docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
```

## 6. Cleanup

Delete the CloudFormation stack when done:

```bash
aws cloudformation delete-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator
```

If you used the Secrets Manager template, the stack does not delete your Secrets Manager secret. Delete or rotate the secret separately if no longer needed.
