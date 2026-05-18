# Marketplace Draft Product Description — KRYP Labs Orbs Boyar AWS Launcher

## Product title

KRYP Labs Orbs Boyar Validator Launcher

## Short description

Launch an Orbs Boyar validator node on AWS from a prebuilt KRYP Labs AMI with CloudFormation-based first-boot configuration.

## Long description

KRYP Labs Orbs Boyar Validator Launcher helps operators deploy an Orbs Boyar validator node on AWS using a prebuilt AMI and CloudFormation templates.

The AMI includes stable non-secret components such as Ubuntu, Docker, the Boyar binary, and installer files. Validator-specific runtime configuration is provided at first boot, so private keys and node-specific settings are not baked into the AMI.

The current public early-access version is available in `us-east-2` with public AMI `ami-0bfc554348685c913`. The GitHub Launch Stack flow uses a public CloudFormation template that creates an EC2 instance, Security Group, root EBS volume, and Elastic IP. It starts the validator through `/opt/orbs/ami_creator/install.sh --configure-and-start`.

For early-access testing, the direct-input template accepts the validator private key as a CloudFormation `NoEcho` parameter. For higher-security production and Marketplace-oriented deployments, KRYP Labs also maintains a Secrets Manager template where operators store the private key in AWS Secrets Manager and pass only the secret ARN to CloudFormation.

## Highlights / features

- Prebuilt public Orbs Boyar Medium AMI.
- CloudFormation-based deployment flow.
- Direct early-access Launch Stack URL for quick testing.
- Optional Secrets Manager flow for stronger secret handling.
- Automatic Elastic IP creation and association.
- Optional reuse of an existing Elastic IP AllocationId for reinstall/recovery.
- Stack outputs for Elastic IP, status URLs, and SSH command.
- Public GitHub documentation and release notes.
- Free software distribution; users pay AWS infrastructure costs.

## Current public assets

Public AMI:

```text
ami-0bfc554348685c913
```

Supported region:

```text
us-east-2
```

GitHub repository:

```text
https://github.com/goldenman-kr/orbs-boyar-aws-launcher
```

Current Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Usage notes

Before launch, customers need:

- AWS account with access to `us-east-2`.
- VPC and public subnet in `us-east-2`.
- EC2 Key Pair in `us-east-2`.
- Ethereum RPC endpoint URL.
- Orbs node address without leading `0x`.
- Validator private key, either entered as a direct NoEcho parameter or stored in AWS Secrets Manager depending on template choice.

After launch, check CloudFormation outputs:

- `ElasticIp`
- `ElasticIpAllocationId`
- `ElasticIpMode`
- `BoyarStatusUrl`
- `ManagementStatusUrl`
- `SSHCommand`

Expected status endpoints:

- `http://<ElasticIp>/services/boyar/status`
- `http://<ElasticIp>:7666/status`

## Security notes

- Private keys are not baked into the AMI.
- Direct-input mode uses CloudFormation `NoEcho`, which hides values in UI/API outputs but is not a dedicated secret store.
- Secrets Manager mode is recommended for higher-security production use.
- Default network access exposes ports `22`, `80`, and `7666` to `0.0.0.0/0`; advanced users should restrict `AccessCidr` if appropriate.
- Users are responsible for EC2 key pair security, validator private key custody, IAM access, AWS account security, and RPC endpoint protection.
- The current template does not configure TLS, DNS, monitoring, alerting, backups, or automatic upgrades.

## Support statement

KRYP Labs support covers launcher documentation, AMI boot/configuration issues, and CloudFormation deployment issues related to this product.

Support does not include customer AWS billing, customer VPC/network design, Ethereum RPC provider incidents, validator key custody mistakes, or reward/economic guarantees.

Final Marketplace listing must include an approved KRYP Labs support email, support URL, and expected response policy.

## Early-access disclaimer

This is an early-access deployment launcher, not a completed AWS Marketplace product. It is currently validated for `us-east-2` only. Customers should test in a controlled environment, review security group exposure, protect private keys, and understand AWS infrastructure costs before using it for production validator operations.


## Marketplace source-region copy

- Region: `us-east-1`
- AMI ID: `ami-0c8d6f2b1c7acdc67`
- Snapshot ID: `snap-0ed6a777975da2ea5`
- Visibility: public
- Purpose: AWS Marketplace source-region registration preparation
