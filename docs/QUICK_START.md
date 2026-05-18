# Quick Start

This guide launches the KRYP Labs Orbs Boyar AWS Launcher release candidate with the public GitHub Launch Stack flow.

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

[![Launch Stack](https://img.shields.io/badge/Launch%20Stack-us--east--2-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fraw.githubusercontent.com%2Fgoldenman-kr%2Forbs-boyar-aws-launcher%2Fmain%2Fcloudformation%2Ftemplate-medium-ami-secrets.yaml)

If you prefer to inspect the template first, open:

```text
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-secrets.yaml
```

## 3. Fill stack parameters

Required parameters:

- `VpcId` — VPC in `us-east-2`
- `SubnetId` — public subnet in `us-east-2`
- `SSHLocation` — your public IP CIDR, preferably `/32`
- `KeyName` — EC2 Key Pair in `us-east-2`
- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — validator node address, 40 hex characters, no `0x`
- `PrivateKeySecretArn` — ARN of the Secrets Manager secret

Defaults:

- `ImageId`: `ami-06d0ede0e331a96d2`
- `InstanceType`: `r5.large`
- `VolumeSize`: `256`

Acknowledge `CAPABILITY_IAM` because the stack creates an EC2 IAM role and instance profile.

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
