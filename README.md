# KRYP Labs Orbs Boyar AWS Launcher

Public early-access release for launching an Orbs Boyar validator node on AWS with CloudFormation.

This package is the **KRYP Labs** GitHub Launch Stack early-access flow. It is intended to evolve toward a future free AWS Marketplace distribution.

## Current early-access status

- GitHub Launch Stack region support: `us-east-2` only
- Public AMI ID for GitHub Launch Stack: `ami-0bfc554348685c913`
- Marketplace source-region AMI: `ami-0c8d6f2b1c7acdc67` in `us-east-1`
- GitHub Launch Stack snapshot: `snap-0ecb5473ac7987f1f` retained by the publisher
- Marketplace source snapshot: `snap-0ed6a777975da2ea5` retained by the publisher
- Public AMI status: publicly launchable in `us-east-2` for early-access testing
- Preferred early-access template: `cloudformation/template-medium-ami-direct.yaml`
- Higher-security production template: `cloudformation/template-medium-ami-secrets.yaml`
- Marketplace submission: not yet started
- Distribution mode: GitHub Launch Stack using a public S3 TemplateURL
- AWS costs: users pay their own EC2, EBS, data transfer, Elastic IP, and optional Secrets Manager costs

## What this launcher does

The CloudFormation template launches one EC2 instance from a validated Medium AMI and configures an Orbs Boyar validator node at first boot.

The AMI already contains the stable non-secret components: Ubuntu 22.04, Docker, the Boyar binary, and installer files. First boot only injects runtime configuration and starts the node.

## Required user inputs

You must provide:

- `VpcId` — target VPC
- `SubnetId` — public subnet in `us-east-2`
- `AccessCidr` — CIDR allowed to access all exposed ports (`22`, `80`, and `7666`); default `0.0.0.0/0`. Advanced users can restrict it later.
- `ExistingEipAllocationId` — optional existing Elastic IP AllocationId for reinstall/recovery; leave empty to create a new EIP automatically.
- `KeyName` — existing EC2 Key Pair name for SSH access
- `InstanceType` — default `r5.large`
- `VolumeSize` — default `256` GiB
- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — 40 hex characters, no `0x`
- `PrivateKeyNoLeading0x` — direct NoEcho private key parameter, 64 hex characters with no `0x`

For the retained Secrets Manager template, the secret may contain either the raw 64-hex private key string or JSON with `PRIVATE_KEY_NO_LEADING_0x`, `privateKeyNoLeading0x`, or `privateKey`.

## AWS resources created

The direct-input template creates:

- One EC2 instance
- One Security Group
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

**Recommended delivery strategy:** KRYP Labs publishes the template to a dedicated public-read S3 object in `us-east-2`. This is more reliable for CloudFormation `TemplateURL` than GitHub raw URLs, while the same template remains version-controlled in GitHub for review.

**Tradeoffs:** S3 delivery is accepted directly by CloudFormation and can be controlled with bucket policy, versioning, content type, and cache settings. GitHub remains the source repository, but GitHub raw URLs are no longer the primary Launch Stack delivery path because CloudFormation does not accept them reliably in all flows.

[![Launch Stack](https://img.shields.io/badge/Launch%20Stack-us--east--2-orange?logo=amazon-aws)](https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml)

Direct Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

Public S3 direct-input template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct.yaml
```

Public S3 Secrets Manager template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

GitHub source templates for review:

```text
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-direct.yaml
https://raw.githubusercontent.com/goldenman-kr/orbs-boyar-aws-launcher/main/cloudformation/template-medium-ami-secrets.yaml
```

### Launch prerequisites

- AWS account with access to `us-east-2`
- VPC and public subnet in `us-east-2`
- EC2 Key Pair in `us-east-2`
- Orbs validator private key for direct NoEcho input, or an AWS Secrets Manager secret in `us-east-2` if using the Secrets Manager template
- Ethereum RPC endpoint URL
- Orbs node address without leading `0x`

### Required AWS permissions

The launching principal needs permission to create and manage the stack resources:

- CloudFormation stack operations
- EC2 instance, Security Group, and EBS root volume operations
- IAM Role, Instance Profile, and inline policy creation; the stack requires `CAPABILITY_IAM`
- Permission to pass the created IAM role to EC2

The EC2 instance role created by the stack receives only `secretsmanager:GetSecretValue` for the specified `PrivateKeySecretArn`.

### Private key input modes

Recommended for early-access testing: use the direct-input template and enter `PrivateKeyNoLeading0x` as a NoEcho parameter.

Recommended for higher-security production and future Marketplace packaging: use the Secrets Manager template, create the private key secret before clicking Launch Stack, and pass only the secret ARN.

### Elastic IP behavior

By default, the direct-input template automatically creates and associates an Elastic IP with the validator instance. Stack outputs use this stable `ElasticIp` value for Boyar status, management status, and SSH examples. Record the `ElasticIp` and `ElasticIpAllocationId` outputs after launch. When the stack creates the EIP, CloudFormation releases it automatically on stack deletion. Advanced users can pass `ExistingEipAllocationId` to preserve the same node IP across reinstall/recovery; in that mode CloudFormation associates the existing EIP but does not release it when the stack is deleted, and users are responsible for any retained EIP charges.

### Network access default

By default, the template exposes SSH (`tcp/22`) and the Boyar/status endpoints (`tcp/80`, `tcp/7666`) to `0.0.0.0/0`. This keeps the public node endpoints reachable for blockchain/node accessibility and simplifies early-access testing. Users are responsible for protecting their EC2 key pair, validator secret, and AWS account. Advanced users can restrict `AccessCidr` to a narrower CIDR such as their own IP `/32`.

### Supported region

This early-access GitHub Launch Stack supports `us-east-2` only. The official public Launch Stack AMI ID is `ami-0bfc554348685c913`. A public Marketplace source-region copy is prepared in `us-east-1` as `ami-0c8d6f2b1c7acdc67` for future AWS Marketplace registration.

### Estimated AWS costs

The launcher software is free, but AWS infrastructure costs apply, primarily EC2 `r5.large`, a 256 GiB gp3 root volume, data transfer, and Secrets Manager secret storage/API calls.

## Launch steps

1. Create or select a VPC and public subnet in `us-east-2`.
2. Create or select an EC2 Key Pair.
3. Create a Secrets Manager secret containing the validator private key.
4. Launch the CloudFormation template:

```bash
aws cloudformation create-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator \
  --template-body file://cloudformation/template-medium-ami-secrets.yaml \
  --capabilities CAPABILITY_IAM \
  --parameters \
    ParameterKey=VpcId,ParameterValue=<vpc-id> \
    ParameterKey=SubnetId,ParameterValue=<subnet-id> \
    ParameterKey=AccessCidr,ParameterValue=0.0.0.0/0 \
    ParameterKey=KeyName,ParameterValue=<ec2-key-pair-name> \
    ParameterKey=EthereumEndpoint,ParameterValue=<ethereum-rpc-url> \
    ParameterKey=NodeAddressWithNoLeading0x,ParameterValue=<40-hex-node-address> \
    ParameterKey=PrivateKeyNoLeading0x,ParameterValue=<64-hex-private-key-without-0x>
```

5. Wait for stack creation to complete.
6. Open the stack outputs and check the Boyar and management status URLs.

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
sudo systemctl status boyar.service --no-pager --full
sudo docker service ls
sudo docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
```

## Delete and cleanup

To delete the launcher stack:

```bash
aws cloudformation delete-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator
```

After deletion, confirm:

- EC2 instance is terminated
- Security Group is deleted
- IAM Role and Instance Profile are deleted
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

- Current AMI is available only in `us-east-2`.
- The template is a release candidate, not a public Marketplace listing.
- Direct-input users enter the private key as a NoEcho CloudFormation parameter; Secrets Manager users must create and manage the secret separately.
- HTTPS/TLS, domain setup, monitoring dashboards, and alerting are not included.
