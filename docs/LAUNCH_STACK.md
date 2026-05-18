# Launch Stack Guide

This guide launches the KRYP Labs Orbs Boyar AWS Launcher release candidate in `us-east-2`.

## Template delivery

CloudFormation Launch Stack uses the public S3 template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-secrets.yaml
```

The GitHub repository remains the source of truth for review and pull requests. The S3 object is the CloudFormation delivery artifact because CloudFormation accepts S3 `TemplateURL` reliably.

## Prerequisites

- AWS CLI configured for the target AWS account
- A VPC and public subnet in `us-east-2`
- An EC2 Key Pair in `us-east-2`
- A Secrets Manager secret in `us-east-2` containing the validator private key
- Validator node address without leading `0x`
- Ethereum RPC endpoint URL

## Create the private key secret

The CloudFormation template expects an existing Secrets Manager secret ARN. The secret value may be either:

- a raw 64-hex private key string, or
- JSON containing `PRIVATE_KEY_NO_LEADING_0x`, `privateKeyNoLeading0x`, or `privateKey`.

Do not paste real private keys into shell history if your shell records commands. Prefer creating the secret through a secured workflow or an interactive file that is deleted immediately afterward.

## Launch

```bash
aws cloudformation create-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator \
  --template-url https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml \
  --capabilities CAPABILITY_IAM \
  --parameters \
    ParameterKey=VpcId,ParameterValue=<vpc-id> \
    ParameterKey=SubnetId,ParameterValue=<subnet-id> \
    ParameterKey=AccessCidr,ParameterValue=0.0.0.0/0 \
    ParameterKey=KeyName,ParameterValue=<ec2-key-pair-name> \
    ParameterKey=EthereumEndpoint,ParameterValue=<ethereum-rpc-url> \
    ParameterKey=NodeAddressWithNoLeading0x,ParameterValue=<40-hex-node-address> \
    ParameterKey=PrivateKeySecretArn,ParameterValue=<secret-arn>
```

Wait:

```bash
aws cloudformation wait stack-create-complete \
  --region us-east-2 \
  --stack-name orbs-boyar-validator
```

Get outputs:

```bash
aws cloudformation describe-stacks \
  --region us-east-2 \
  --stack-name orbs-boyar-validator \
  --query 'Stacks[0].Outputs'
```

## Verify

Expected:

- Stack status is `CREATE_COMPLETE`
- Boyar status endpoint returns HTTP 200
- Management status endpoint returns HTTP 200
- `boyar.service` is active/running
- `management-service` is healthy

## Delete

```bash
aws cloudformation delete-stack \
  --region us-east-2 \
  --stack-name orbs-boyar-validator
```

The stack does not delete your Secrets Manager secret. Delete or rotate it separately when appropriate.
