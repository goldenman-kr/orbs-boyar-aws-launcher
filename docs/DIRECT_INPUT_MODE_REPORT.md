# Direct Input Mode Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Public AMI: `ami-0bfc554348685c913`
- New template: `cloudformation/template-medium-ami-direct.yaml`
- Existing Secrets Manager template retained: `cloudformation/template-medium-ami-secrets.yaml`
- CloudFormation stacks deployed: no
- New AWS compute resources created: no

## Summary

A separate direct-input CloudFormation template was added for early-access/public testing. It allows users to enter the Orbs validator private key directly in the CloudFormation Create Stack UI as a `NoEcho` parameter named `PrivateKeyNoLeading0x`.

The existing Secrets Manager template remains available for higher-security production and future Marketplace flows.

## Direct input template

Public S3 TemplateURL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct.yaml
```

Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Parameters

The direct-input template accepts:

- `VpcId`
- `SubnetId`
- `AccessCidr`
- `KeyName`
- `ImageId`
- `InstanceType`
- `VolumeSize`
- `EthereumEndpoint`
- `NodeAddressWithNoLeading0x`
- `PrivateKeyNoLeading0x` (`NoEcho: true`)

It does not require `PrivateKeySecretArn` and does not create an EC2 role for Secrets Manager access.

## Runtime behavior

On first boot, UserData writes `/opt/orbs/ami_creator/.env` from CloudFormation parameters and runs:

```text
/opt/orbs/ami_creator/install.sh --configure-and-start
```

The private key is not printed in template validation output, reports, or logs generated during this update.

## Security note

`NoEcho` hides the private key in CloudFormation UI and API outputs, but it does not make CloudFormation a secret manager. The value still exists temporarily in CloudFormation parameter handling during stack creation.

Recommended usage:

- Early-access users: direct-input template for the simplest Launch Stack flow.
- Marketplace/public production users: Secrets Manager template for stronger secret handling.

## Validation performed

- Local CloudFormation `validate-template` passed for `template-medium-ami-direct.yaml`.
- Public S3 TemplateURL was uploaded and fetched successfully.
- CloudFormation `validate-template --template-url` passed for the public direct template.
- Parameters rendered correctly and include `PrivateKeyNoLeading0x`.
- Parameters do not include `PrivateKeySecretArn`.
- Template/docs scan found no `set -x`.
- Template/docs scan found no pre-signed URLs.
- Template/docs scan found no exact local secret values from the build environment.

## Remaining limitations

- Direct-input mode is intended for early-access usability, not the final high-security posture.
- Region support remains `us-east-2` only.
- Users are responsible for protecting their validator private key, EC2 key pair, AWS account, and stack access.
- No CloudFormation stack was deployed as part of this validation.
