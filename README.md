# Orbs Boyar AWS Launcher

Public early-access release for launching an Orbs Boyar validator node on AWS with CloudFormation.

This package is the **KRYP Labs** GitHub Launch Stack early-access flow. It is intended to evolve toward a future free AWS Marketplace distribution.

## 🚀 Launch Orbs Network Full Node on AWS

Deploy with only three required values.

<a href="https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-node-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github.yaml" target="_blank" rel="noopener noreferrer"><img src="https://img.shields.io/badge/Launch%20Stack-us--east--1-orange?style=for-the-badge&logo=amazonaws" alt="Launch Stack us-east-1" /></a>

Plain link:

- <a href="https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-node-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github.yaml" target="_blank" rel="noopener noreferrer">Launch Stack in US East 1 / N. Virginia (`us-east-1`)</a>

> [!TIP]
> Need another AWS Region? Use the AWS Marketplace listing instead: https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3

Required inputs:

- Ethereum RPC endpoint
- Orbs node address
- Orbs private key

## Current early-access status

- GitHub Launch Stack region support: `us-east-1` only
- Latest GitHub Launch Stack AMI version: `v0.1.4`
- Public AMI ID for GitHub Launch Stack in `us-east-1`: `ami-05171a26aaa49fa0d`
- Public AMI status: publicly launchable in `us-east-1` for early-access testing
- GitHub easy-mode template: `cloudformation/template-medium-ami-direct-autonet-github.yaml` for `us-east-1`
- Marketplace review template: `cloudformation/template-medium-ami-direct-autonet.yaml`
- Higher-security production template: `cloudformation/template-medium-ami-secrets.yaml`
- Marketplace submission: available separately through AWS Marketplace
- Distribution mode: GitHub Launch Stack using a public S3 TemplateURL
- AWS costs: users pay their own EC2, EBS, data transfer, Elastic IP, and optional Secrets Manager costs

## What this launcher does

The CloudFormation template launches one EC2 instance from a validated Medium AMI and configures an Orbs Boyar validator node at first boot.

The AMI already contains the stable non-secret components: Ubuntu 22.04, Docker, the Boyar binary, and installer files. First boot only injects runtime configuration and starts the node.

## Required user inputs

Users only need to provide three values for the default Launch Stack flow:

- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — 40 hex characters, no `0x`
- `PrivateKeyNoLeading0x` — direct NoEcho private key parameter, 64 hex characters with no `0x`

Everything else is automatically provisioned or optional:

- VPC and public subnet are created automatically.
- Internet Gateway, route table, default route, and Security Group are created automatically.
- Elastic IP is created automatically, with optional `ExistingEipAllocationId` reuse for advanced reinstall/recovery.
- SSH Key Pair handling is automatic in both GitHub and Marketplace templates. Select an existing regional `KeyName` to reuse it, or leave it empty and the stack creates a new EC2 Key Pair automatically.
- `SshAccessCidr` controls SSH (`22`) and defaults to `0.0.0.0/0` for easy validation/testing; SSH remains key-only and password login is disabled in the AMI. `AccessCidr` controls node/status endpoints (`80`, `7666`). GitHub easy-mode defaults it to `0.0.0.0/0` for straightforward validation; Marketplace review keeps the stricter default in its separate template.

For the retained Secrets Manager template, the secret may contain either the raw 64-hex private key string or JSON with `PRIVATE_KEY_NO_LEADING_0x`, `privateKeyNoLeading0x`, or `privateKey`.

## AWS resources created

The autonet direct-input template creates:

- One VPC
- One public subnet
- One Internet Gateway
- One route table and default route
- One Security Group
- One EC2 instance
- One Elastic IP allocated and associated with the instance, unless `ExistingEipAllocationId` is provided
- One root EBS volume attached to the instance, with `DeleteOnTermination: true`

The retained Secrets Manager template also creates an EC2 IAM Role, Instance Profile, and inline policy allowing `secretsmanager:GetSecretValue` only on `PrivateKeySecretArn`. It does not create the Secrets Manager secret.

## Private key handling

The default early-access Launch Stack flow uses direct private key input:

1. You enter `PrivateKeyNoLeading0x` in the CloudFormation Create Stack UI.
2. The parameter is marked `NoEcho`, so CloudFormation hides it in the UI/API outputs.
3. First boot writes `/opt/orbs/ami_creator/.env` locally with mode `0600` and runs the installer.
4. Installer logs redact private key values.

This is easier for public early-access testing, but `NoEcho` is not the same as a dedicated secret store. The value still exists temporarily in CloudFormation parameter handling during stack creation. Do not paste private keys into stack descriptions, tickets, logs, screenshots, or chat messages.

The existing Secrets Manager template is retained for higher-security production and future Marketplace use. In that mode, users create a Secrets Manager secret first and pass only `PrivateKeySecretArn` to CloudFormation.


## Launch Stack

The public Launch Stack flow now uses a public S3 HTTPS object URL for the CloudFormation template.

**Recommended delivery strategy:** KRYP Labs publishes the template to a dedicated public-read public S3 object. This is more reliable for CloudFormation `TemplateURL` than GitHub raw URLs, while the same template remains version-controlled in GitHub for review.

**Tradeoffs:** S3 delivery is accepted directly by CloudFormation and can be controlled with bucket policy, versioning, content type, and cache settings. GitHub remains the source repository, but GitHub raw URLs are no longer the primary Launch Stack delivery path because CloudFormation does not accept them reliably in all flows.

### GitHub Launch Stack button

Use this button to launch in **US East 1 / N. Virginia (`us-east-1`)**. GitHub Launch Stack is intentionally limited to `us-east-1`.

[![Launch Stack us-east-1](https://img.shields.io/badge/Launch%20Stack-us--east--1-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-node-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github.yaml)

Direct Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?stackName=orbs-node-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet-github.yaml
```

> [!TIP]
> For Korea, Japan, US West 1, or other AWS Regions, use the AWS Marketplace listing instead: https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3

Public S3 direct-input template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet-github.yaml
```

Public S3 Secrets Manager template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

GitHub source templates for review:

```text
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-direct-autonet-github.yaml
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-secrets.yaml
```

### Launch prerequisites

- AWS account with access to `us-east-1`
- Ethereum RPC endpoint URL
- Orbs node address without leading `0x`
- Orbs validator private key for direct NoEcho input

The GitHub easy-mode and Marketplace AutoNet templates create their own VPC, public subnet, Internet Gateway, route table, Security Group, EC2 instance, Elastic IP, and — when `KeyName` is left empty — an EC2 Key Pair. Existing Key Pair reuse, existing EIP reuse, and custom access CIDR are optional advanced settings.

### Required AWS permissions

The launching principal needs permission to create and manage the stack resources:

- CloudFormation stack operations
- EC2 instance, Security Group, and EBS root volume operations
- EC2, VPC, subnet, Internet Gateway, route table, Security Group, EBS root volume, Elastic IP, and EC2 Key Pair operations

The default autonet direct-input template does not create IAM roles and does not require `CAPABILITY_IAM`. If `KeyName` is left empty, CloudFormation creates an EC2 Key Pair and stores the generated private key in Systems Manager Parameter Store under `/ec2/keypair/<key-pair-id>`; retrieving it requires `ssm:GetParameter` with decryption permission. The retained Secrets Manager template creates an EC2 instance role with only `secretsmanager:GetSecretValue` for the specified `PrivateKeySecretArn`.

### Private key input modes

Recommended for early-access testing: use the direct-input template and enter `PrivateKeyNoLeading0x` as a NoEcho parameter.

Recommended for higher-security production and future Marketplace packaging: use the Secrets Manager template, create the private key secret before clicking Launch Stack, and pass only the secret ARN.

### Elastic IP behavior

By default, the direct-input template automatically creates and associates an Elastic IP with the validator instance. Stack outputs use this stable `ElasticIp` value for Boyar status, management status, and SSH examples. Record the `ElasticIp` and `ElasticIpAllocationId` outputs after launch. When the stack creates the EIP, CloudFormation releases it automatically on stack deletion. Advanced users can pass `ExistingEipAllocationId` to preserve the same node IP across reinstall/recovery; in that mode CloudFormation associates the existing EIP but does not release it when the stack is deleted, and users are responsible for any retained EIP charges.

Key Pair handling follows the same reuse-or-create pattern. Select an existing regional `KeyName` to reuse it; leave `KeyName` empty to auto-create a stack-managed EC2 Key Pair. Outputs show `AppliedKeyName` and `KeyPairMode`. If a key pair is auto-created, outputs also show the SSM parameter name and retrieval command for the private key. Auto-created key pairs and their private-key SSM parameters are deleted with the stack; existing key pairs are never deleted by this stack.

### Network access defaults

For AWS Marketplace validation, use `cloudformation/template-medium-ami-direct-autonet.yaml`; `KeyName` is optional and supports the same reuse-or-auto-create behavior as the GitHub template. `SshAccessCidr` controls reviewer/operator SSH reachability. For GitHub easy-mode launches, use the `us-east-1` GitHub template. SSH remains key-only because password authentication is disabled in the AMI. For production, restrict `SshAccessCidr` to a trusted operator IP/CIDR.

GitHub easy-mode node/status endpoint access defaults to `AccessCidr=0.0.0.0/0` so the launched node can be validated immediately. Marketplace review uses the separate `cloudformation/template-medium-ami-direct-autonet.yaml` template and keeps the stricter `AccessCidr=127.0.0.1/32` default. For production, restrict `AccessCidr` to a trusted public IP as `x.x.x.x/32` or another CIDR range you control.

### Supported regions

This early-access GitHub Launch Stack supports `us-east-1` only. For other AWS Regions, use the AWS Marketplace listing: https://aws.amazon.com/marketplace/pp?sku=51c282skmep4qmamtmspsi8f3

### Estimated AWS costs

The launcher software is free, but AWS infrastructure costs apply, primarily EC2 `r5.large`, a 256 GiB gp3 root volume, data transfer, and Secrets Manager secret storage/API calls if using the Secrets Manager template.

## Launch steps

1. Prepare the three required values:
   - Ethereum RPC endpoint
   - Orbs node address without leading `0x`
   - Orbs private key without leading `0x`
2. Click the Launch Stack button, or launch the autonet template from CLI:

```bash
aws cloudformation create-stack   --region us-east-1   --stack-name orbs-node-validator   --template-body file://cloudformation/template-medium-ami-direct-autonet-github.yaml   --parameters     ParameterKey=EthereumEndpoint,ParameterValue=<ethereum-rpc-url>     ParameterKey=NodeAddressWithNoLeading0x,ParameterValue=<40-hex-node-address>     ParameterKey=PrivateKeyNoLeading0x,ParameterValue=<64-hex-private-key-without-0x>
```

3. Wait for stack creation to complete.
4. Open the stack outputs and check `ElasticIp`, `BoyarStatusUrl`, `ManagementStatusUrl`, `AppliedKeyName`, `KeyPairMode`, and `SSHCommand`. If the stack auto-created the key pair, also save the private key using `AutoCreatedPrivateKeyRetrievalCommand` before relying on SSH access.

## Verify after launch

Expected results:

- CloudFormation stack status: `CREATE_COMPLETE`
- `http://<ElasticIp>/services/boyar/status`: HTTP 200
- `http://<ElasticIp>:7666/status`: HTTP 200
- `boyar.service`: active/running
- `management-service`: healthy

SSH example:

```bash
ssh -i <key-file.pem> ubuntu@<ElasticIp>
# If KeyName was empty, first run the AutoCreatedPrivateKeyRetrievalCommand output to save the generated .pem file.
# Expected: key-based login succeeds. Password login is rejected because PasswordAuthentication is disabled.
sudo systemctl status boyar.service --no-pager --full
sudo docker service ls
sudo docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
```

## Delete and cleanup

To delete the launcher stack:

```bash
aws cloudformation delete-stack \
  --region us-east-1 \
  --stack-name orbs-node-validator
```

After deletion, confirm:

- EC2 instance is terminated
- Security Group is deleted
- Stack-managed EC2 Key Pair and generated private-key SSM parameter are deleted if `KeyName` was left empty; reused existing key pairs are retained
- EBS root volume is deleted
- Auto-created Elastic IP is released; reused EIP is retained

If you used the Secrets Manager template, the stack does not delete your Secrets Manager secret. Delete or rotate it separately if no longer needed.

## Expected AWS costs

This launcher is intended to be free as software, but AWS infrastructure costs still apply. Main cost drivers:

- EC2 instance, default `r5.large`
- EBS gp3 root volume, default `256` GiB
- Data transfer
- AWS Secrets Manager secret monthly storage/API calls
- CloudWatch or other monitoring if users add it later
- Elastic IP while the stack exists; auto-created EIPs are released when the stack is deleted, while reused EIPs remain user-managed and may incur charges.

## Limitations

- Current GitHub Launch Stack AMI version is available in `us-east-1` only. For other AWS Regions, use AWS Marketplace.
- GitHub Launch Stack is limited to `us-east-1`; use AWS Marketplace for other supported AWS Regions.
- Direct-input users enter the private key as a NoEcho CloudFormation parameter; Secrets Manager users must create and manage the secret separately.
- HTTPS/TLS, domain setup, monitoring dashboards, and alerting are not included.
