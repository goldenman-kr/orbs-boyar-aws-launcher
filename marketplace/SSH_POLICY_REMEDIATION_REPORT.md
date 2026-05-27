# Marketplace SSH Policy Remediation Report

## Summary

AWS Marketplace version update failed on two SSH/network policy checks. Both issues have been remediated.

Reviewer failures addressed:

1. Do not alter SSH configuration after deployment; Linux AMIs must disable SSH password authentication and root password logins.
2. Do not set default CIDR open to the world on ingress; remove default `0.0.0.0/0` and ask the customer to specify desired CIDR.

## Remediation applied

### Item 1: SSH policy

Removed the CloudFormation `UserData` SSH mutation block from:

```text
cloudformation/template-medium-ami-direct-autonet.yaml
```

The Marketplace template no longer writes `/etc/ssh/sshd_config`, `/etc/ssh/sshd_config.d/*`, restarts sshd, or changes SSH settings during deployment.

Instead, SSH hardening is baked into a refreshed AMI before publication:

```text
/etc/ssh/sshd_config.d/99-aws-marketplace-key-only.conf
```

Baked settings:

```text
PasswordAuthentication no
KbdInteractiveAuthentication no
ChallengeResponseAuthentication no
PubkeyAuthentication yes
PermitRootLogin prohibit-password
```

`sshd -t` passed during baking and sshd is enabled in the AMI.

### Item 2: SshAccessCidr default

Removed the default value from `SshAccessCidr` in the Marketplace template.

Current parameter behavior:

```yaml
SshAccessCidr:
  Type: String
  Description: CIDR allowed to reach SSH on TCP/22. Please set CIDR to x.x.x.x/32 to allow one specific IP address access, 0.0.0.0/0 to allow all IP addresses access, or another CIDR range you control. SSH remains key-only and password authentication is disabled in the AMI.
```

There is no `Default: 0.0.0.0/0`.

## New AMI IDs

New AMIs were required because Marketplace version refresh appears to require a changed AMI ID.

- us-east-1: `ami-05171a26aaa49fa0d`

The current us-east-1 AMI is public and available.

Source lineage:

- Copied from previous validated us-east-1 AMI for Marketplace version update

Snapshots:

- us-east-1: `snap-0e0b5e6379b74d120`

## Validation results

### New AMI SSH validation

Validation was performed against a fresh instance launched from:

```text
ami-05171a26aaa49fa0d
```

Observed:

```text
tcp22-open
ssh-key-login-ok
sshd active
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
baked-dropin-present
password_attempt_exit_code=255
```

Password login was rejected.

### Marketplace template validation

Local validation passed:

```text
aws cloudformation validate-template --region us-east-1 --template-body file://cloudformation/template-medium-ami-direct-autonet.yaml
```

Public TemplateURL validation passed after S3 upload:

```text
aws cloudformation validate-template --region us-east-1 --template-url https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

Rendered parameter checks:

```text
ImageId default: ami-05171a26aaa49fa0d
KeyName: optional; existing-key reuse or auto-created Key Pair when omitted
SshAccessCidr: required, no default
AccessCidr default: 127.0.0.1/32
VpcId parameter: absent
SubnetId parameter: absent
```

## Current Marketplace-visible template URL

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

## Marketplace resubmission guidance

Use the new version AMI ID required by Marketplace:

```text
us-east-1 source-region AMI: ami-05171a26aaa49fa0d
```

Use the stable Marketplace CloudFormation template URL above.

When configuring the delivery option, provide the IAM access role ARN:

```text
arn:aws:iam::617775257107:role/AWSMarketplaceAmiIngestionRole
```

The template now requires the customer/reviewer to supply:

- `KeyName`
- `SshAccessCidr`
- `EthereumEndpoint`
- `NodeAddressWithNoLeading0x`
- `PrivateKeyNoLeading0x`

This directly addresses both Marketplace review failures without changing product identity.
