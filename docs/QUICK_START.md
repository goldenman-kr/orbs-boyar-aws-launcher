# Quick Start

This guide launches the KRYP Labs Orbs Boyar AWS Launcher release candidate with the public S3-backed AutoNet direct-input Launch Stack flow.

## 1. Prepare the private key value

For early-access testing, the default Launch Stack flow accepts the validator private key directly as `PrivateKeyNoLeading0x`.

Accepted format:

```text
<64-hex-private-key-without-0x>
```

The CloudFormation parameter is marked `NoEcho`, so the value is hidden in the Create Stack UI and API outputs. However, it still exists temporarily in CloudFormation parameter handling during stack creation. Do not paste real private keys into issue trackers, screenshots, logs, shell history, or chat.

For higher-security production use, use the separate Secrets Manager template instead.

## 2. Click Launch Stack

Launch in `us-east-1`:

[![Launch Stack](https://img.shields.io/badge/Launch%20Stack-us--east--2-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-1.s3.us-east-1.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github-us-east-1.yaml)

If you prefer to inspect the exact CloudFormation template before launching, open the public S3 template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-1.s3.us-east-1.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet-github-us-east-1.yaml
```

## 3. Fill stack parameters

Required parameters:

- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — validator node address, 40 hex characters, no `0x`
- `PrivateKeyNoLeading0x` — direct NoEcho private key parameter, 64 hex characters with no `0x`

Optional advanced parameters include `SshAccessCidr`, `AccessCidr`, `ExistingEipAllocationId`, `KeyName`, `InstanceType`, `VolumeSize`, and `ImageId`. VPC and subnet selection are no longer required; the template creates them automatically.

Defaults:

- `ImageId`: `ami-0a56576d054e9228e` (public AMI in `us-east-1`)
- `InstanceType`: `r5.large`
- `VolumeSize`: `256`
- `SshAccessCidr`: `0.0.0.0/0` for Marketplace SSH validation; restrict this to a trusted operator IP/CIDR for production
- `AccessCidr`: `127.0.0.1/32` for secure-by-default node/status endpoint access
- `KeyName`: optional existing regional EC2 Key Pair. Leave empty to auto-create and use a stack-managed Key Pair.

No IAM capability acknowledgement is required for the default direct AutoNet template.

Both GitHub and Marketplace launches support automatic SSH Key Pair handling: leave `KeyName` empty to create a stack-managed Key Pair, or select an existing regional Key Pair to reuse it. If the stack creates a Key Pair, use the `AutoCreatedPrivateKeyRetrievalCommand` output to save the generated private key. `SshAccessCidr=0.0.0.0/0` is reachable by default and password authentication is disabled; restrict `SshAccessCidr` to your trusted public IP/CIDR for production. Node/status endpoint access remains secure-by-default with `AccessCidr=127.0.0.1/32`; widen it only when you intentionally want external endpoint access. The separate Marketplace template keeps the stable filename `template-medium-ami-direct-autonet.yaml`.

## 4. Record the Elastic IP

By default, the stack automatically creates and associates an Elastic IP. Record the `ElasticIp` and `ElasticIpAllocationId` stack outputs; the status URLs and SSH command use this address. If `ExistingEipAllocationId` is empty, CloudFormation releases the auto-created EIP when the stack is deleted. Advanced users can provide an existing AllocationId to preserve the same node IP across reinstall/recovery; reused EIPs are not released by this stack and may incur charges. Key Pair cleanup works the same way: auto-created Key Pairs are deleted with the stack, while selected existing Key Pairs are retained.

## 5. Verify endpoints

After stack status is `CREATE_COMPLETE`, check stack outputs for:

- `ElasticIp`
- `ElasticIpAllocationId`
- `ElasticIpMode`
- `BoyarStatusUrl`
- `ManagementStatusUrl`
- `AppliedKeyName`
- `KeyPairMode`
- `AutoCreatedPrivateKeySsmParameterName` and `AutoCreatedPrivateKeyRetrievalCommand` when `KeyName` was left empty
- `SSHCommand`

Expected:

```text
/services/boyar/status HTTP 200
:7666/status HTTP 200
```

Optional SSH / Marketplace reviewer verification:

```bash
ssh -i <key-file.pem> ubuntu@<ElasticIp>
# Expected: key-based login succeeds. Password login is rejected because PasswordAuthentication is disabled.
sudo systemctl status boyar.service --no-pager --full
sudo docker service ls
sudo docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
```

## 6. Cleanup

Delete the CloudFormation stack when done:

```bash
aws cloudformation delete-stack \
  --region us-east-1 \
  --stack-name orbs-boyar-validator
```

If you used the Secrets Manager template, the stack does not delete your Secrets Manager secret. Delete or rotate the secret separately if no longer needed.
