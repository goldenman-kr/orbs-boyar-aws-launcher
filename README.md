# KRYP Labs Orbs Boyar AWS Launcher

Release candidate for launching an Orbs Boyar validator node on AWS with CloudFormation.

This package is intended for a future free AWS Marketplace / GitHub Launch Stack distribution under the **KRYP Labs** brand.

## Current release-candidate status

- Region support: `us-east-2` only
- AMI ID: `ami-06d0ede0e331a96d2`
- Snapshot: `snap-0039ee6a089ecb6a2` retained by the publisher
- Preferred template: `cloudformation/template-medium-ami-secrets.yaml`
- Marketplace submission: not yet started

## What this launcher does

The CloudFormation template launches one EC2 instance from a validated Medium AMI and configures an Orbs Boyar validator node at first boot.

The AMI already contains the stable non-secret components: Ubuntu 22.04, Docker, the Boyar binary, and installer files. First boot only injects runtime configuration and starts the node.

## Required user inputs

You must provide:

- `VpcId` — target VPC
- `SubnetId` — public subnet in `us-east-2`
- `SSHLocation` — CIDR allowed to access SSH and status ports, preferably your IP `/32`
- `KeyName` — existing EC2 Key Pair name for SSH access
- `InstanceType` — default `r5.large`
- `VolumeSize` — default `256` GiB
- `EthereumEndpoint` — Ethereum RPC endpoint URL
- `NodeAddressWithNoLeading0x` — 40 hex characters, no `0x`
- `PrivateKeySecretArn` — AWS Secrets Manager secret ARN containing the validator private key

The secret may contain either the raw 64-hex private key string or JSON with one of these keys:

- `PRIVATE_KEY_NO_LEADING_0x`
- `privateKeyNoLeading0x`
- `privateKey`

## AWS resources created

The template creates:

- One EC2 instance
- One Security Group
- One IAM Role for the EC2 instance
- One IAM Instance Profile
- One inline IAM policy allowing `secretsmanager:GetSecretValue` only on `PrivateKeySecretArn`
- One root EBS volume attached to the instance, with `DeleteOnTermination: true`

The template does not create the Secrets Manager secret. Create the secret before launching.

## Private key handling

The private key is not accepted as a raw CloudFormation parameter.

Instead:

1. You store the private key in AWS Secrets Manager.
2. You pass only the secret ARN to CloudFormation.
3. The EC2 instance role reads only that specified secret during first boot.
4. UserData writes `/opt/orbs/ami_creator/.env` locally with mode `0600`.
5. Installer logs redact private key values.

Do not paste private keys into CloudFormation parameters, stack descriptions, tickets, logs, or chat messages.

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
    ParameterKey=SSHLocation,ParameterValue=<your-ip>/32 \
    ParameterKey=KeyName,ParameterValue=<ec2-key-pair-name> \
    ParameterKey=EthereumEndpoint,ParameterValue=<ethereum-rpc-url> \
    ParameterKey=NodeAddressWithNoLeading0x,ParameterValue=<40-hex-node-address> \
    ParameterKey=PrivateKeySecretArn,ParameterValue=<secret-arn>
```

5. Wait for stack creation to complete.
6. Open the stack outputs and check the Boyar and management status URLs.

## Verify after launch

Expected results:

- CloudFormation stack status: `CREATE_COMPLETE`
- `http://<public-ip>/services/boyar/status`: HTTP 200
- `http://<public-ip>:7666/status`: HTTP 200
- `boyar.service`: active/running
- `management-service`: healthy

SSH example:

```bash
ssh -i <key-file.pem> ubuntu@<public-ip>
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
- No Elastic IP was allocated by this template

The template does not delete your Secrets Manager secret. Delete or rotate it separately if no longer needed.

## Expected AWS costs

This launcher is intended to be free as software, but AWS infrastructure costs still apply. Main cost drivers:

- EC2 instance, default `r5.large`
- EBS gp3 root volume, default `256` GiB
- Data transfer
- AWS Secrets Manager secret monthly storage/API calls
- CloudWatch or other monitoring if users add it later

No Elastic IP is allocated by the template.

## Limitations

- Current AMI is available only in `us-east-2`.
- The template is a release candidate, not a public Marketplace listing.
- Users must create and manage the Secrets Manager secret separately.
- HTTPS/TLS, domain setup, monitoring dashboards, and alerting are not included.
