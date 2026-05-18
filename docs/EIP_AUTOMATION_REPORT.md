# Elastic IP Automation Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Public AMI: `ami-0bfc554348685c913`
- Direct template: `cloudformation/template-medium-ami-direct.yaml`
- Secrets Manager template: `cloudformation/template-medium-ami-secrets.yaml`
- Marketplace submission attempted: no
- New AMI created: no
- Existing AMI/snapshot modified or deleted: no

## Summary

The public Launch Stack templates now allocate a new Elastic IP automatically, associate it with the validator EC2 instance, and expose that stable address through CloudFormation outputs.

CloudFormation owns the EIP resource, so deleting the stack releases the EIP automatically.

## Template changes

Added resources:

- `AWS::EC2::EIP`
- `AWS::EC2::EIPAssociation`

Updated outputs:

- `ElasticIp`
- `BoyarStatusUrl` using `ElasticIp`
- `ManagementStatusUrl` using `ElasticIp`
- `SSHCommand` using `ElasticIp`

Ingress remains configurable through `AccessCidr`, with default `0.0.0.0/0` for:

- `tcp/22`
- `tcp/80`
- `tcp/7666`

## Public direct Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Public direct TemplateURL

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct.yaml
```

## Validation

- Local CloudFormation validation: passed for direct and Secrets Manager templates
- Public TemplateURL validation: passed for direct and Secrets Manager templates
- Direct-input private key mode retained with `PrivateKeyNoLeading0x` and `NoEcho: true`
- Secrets Manager template retained for higher-security use
- No `set -x` expected
- No pre-signed URLs expected
- No secrets should be printed in this report

## Test stack result

A direct-input test stack was deployed and deleted to verify EIP behavior.

- Test stack: `orbs-boyar-eip-test-20260518220526`
- EIP allocated: yes
- EIP associated to EC2 instance: yes
- `BoyarStatusUrl` via EIP: HTTP 200
- `ManagementStatusUrl` via EIP: HTTP 200
- Instance reached expected runtime status through the EIP

No secret values are included in this report.

## Cleanup result

- Stack deletion completed successfully.
- EIP allocation was released automatically by CloudFormation.
- No EBS volume remained attached to the test instance.
- Test instance reached `terminated` state.
- No AMI or snapshot was modified or deleted.

## Future enhancement

Allow advanced users to provide an existing EIP `AllocationId` for reinstall/recovery workflows where preserving a known validator endpoint is more important than fully automatic stack-owned EIP lifecycle.
