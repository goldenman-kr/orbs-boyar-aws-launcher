# Quick Start

This guide launches the KRYP Labs Orbs Boyar AWS Launcher release candidate with the public S3-backed Launch Stack flow.

## 1. Create the Secrets Manager secret

Create a Secrets Manager secret in `us-east-2` that contains the validator private key.

Accepted formats:

Raw string:

```text
<64-hex-private-key-without-0x>
```

JSON:

```json
{
  "PRIVATE_KEY_NO_LEADING_0x": "<64-hex-private-key-without-0x>"
}
```

Do not paste real private keys into issue trackers, screenshots, logs, shell history, or chat.

## 2. Click Launch Stack

Launch in `us-east-2`:

[![Launch Stack](https://img.shields.io/badge/Launch%20Stack-us--east--2-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-secrets.yaml)

If you prefer to inspect the exact CloudFormation template before launching, open the public S3 template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

## 3. Fill stack parameters

Required parameters:

- `VpcId` — VPC in `us-east-2`
- `SubnetId` — public subnet in `us-east-2`
- `AccessCidr` — CIDR allowed to access SSH (`22`) and node/status endpoints (`80`, `7666`); default `0.0.0.0/0`
- `KeyName` — EC2 Key Pair in `us-east-2`
- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — validator node address, 40 hex characters, no `0x`
- `PrivateKeySecretArn` — ARN of the Secrets Manager secret

Defaults:

- `ImageId`: `ami-0bfc554348685c913` (public AMI in `us-east-2`)
- `InstanceType`: `r5.large`
- `VolumeSize`: `256`

Acknowledge `CAPABILITY_IAM` because the stack creates an EC2 IAM role and instance profile.

Default network access exposes SSH and node/status endpoints publicly (`0.0.0.0/0`). This is intentional for the launcher default and public node accessibility. You are responsible for securing your EC2 key pair and AWS account. Advanced users can restrict `AccessCidr` to a narrower CIDR if desired.

## 4. Verify endpoints

After stack status is `CREATE_COMPLETE`, check stack outputs for:

- `BoyarStatusUrl`
- `ManagementStatusUrl`

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

## 5. Cleanup

Delete the CloudFormation stack when done:

```bash
aws cloudformation delete-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator
```

The stack does not delete your Secrets Manager secret. Delete or rotate the secret separately if no longer needed.
