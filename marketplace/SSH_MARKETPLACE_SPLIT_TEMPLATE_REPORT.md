# Marketplace SSH Split-Template Remediation Report

## Summary

The SSH remediation has been split into two AutoNet templates so AWS Marketplace review continuity is preserved while GitHub users keep the simplified optional-SSH deployment path.

- Marketplace template filename remains stable: `cloudformation/template-medium-ami-direct-autonet.yaml`
- New GitHub easy-mode template: `cloudformation/template-medium-ami-direct-autonet-github.yaml`
- Both templates preserve AutoNet provisioning and direct NoEcho private-key input.

## Public template URLs

Marketplace review URL, unchanged filename:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

GitHub easy-mode Launch Stack URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet-github.yaml
```

## Marketplace template behavior

Template:

```text
cloudformation/template-medium-ami-direct-autonet.yaml
```

Changes applied:

- `KeyName` is now optional: select an existing regional Key Pair or leave empty for stack-managed auto-creation.
- Empty `Default: ''` was removed.
- Optional `HasKeyName` condition was removed.
- EC2 instance now uses:

```yaml
KeyName: !Ref KeyName
```

- Parameter description now states:

```text
EC2 Key Pair required for SSH access and AWS Marketplace validation.
```

Marketplace SSH posture:

- TCP/22 is controlled by `SshAccessCidr`.
- `SshAccessCidr` default remains `0.0.0.0/0` for Marketplace reviewer reachability.
- SSH is key-only.
- First boot explicitly writes `PasswordAuthentication no` and restarts/enables sshd.

## GitHub easy-mode template behavior

Template:

```text
cloudformation/template-medium-ami-direct-autonet-github.yaml
```

Behavior preserved:

- `KeyName` remains optional with empty default.
- `HasKeyName` condition remains.
- EC2 instance omits `KeyName` when empty.
- `SSHCommand` output always reflects the applied Key Pair; auto-created keys also expose SSM retrieval outputs.
- 3-value UX is preserved: users only need `EthereumEndpoint`, `NodeAddressWithNoLeading0x`, and `PrivateKeyNoLeading0x` for the default launch.

## AutoNet behavior preserved in both templates

Both templates still include:

- Auto-created VPC
- Auto-created public subnet
- Internet Gateway
- Route table and default route
- Security Group
- EC2 instance from the public Medium AMI
- Elastic IP auto-create
- Existing EIP reuse via `ExistingEipAllocationId`
- Direct `PrivateKeyNoLeading0x` with `NoEcho: true`
- Docker Swarm / Boyar runtime first-boot configuration
- SSH hardening drop-in that disables password login

## Validation results

Local validation passed:

```text
aws cloudformation validate-template --region us-east-2 --template-body file://cloudformation/template-medium-ami-direct-autonet.yaml
aws cloudformation validate-template --region us-east-2 --template-body file://cloudformation/template-medium-ami-direct-autonet-github.yaml
```

Public TemplateURL validation passed after S3 upload:

```text
aws cloudformation validate-template --region us-east-2 --template-url https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
aws cloudformation validate-template --region us-east-2 --template-url https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet-github.yaml
```

Parameter rendering confirmed:

Marketplace template:

```text
KeyName default: empty / auto-create when omitted
VpcId parameter: absent
SubnetId parameter: absent
Required runtime values: EthereumEndpoint, NodeAddressWithNoLeading0x, PrivateKeyNoLeading0x
AccessCidr default: 127.0.0.1/32
SshAccessCidr default: 0.0.0.0/0
```

GitHub easy-mode template:

```text
KeyName default: empty string / optional
VpcId parameter: absent
SubnetId parameter: absent
Required runtime values: EthereumEndpoint, NodeAddressWithNoLeading0x, PrivateKeyNoLeading0x
AccessCidr default: 127.0.0.1/32
SshAccessCidr default: 0.0.0.0/0
```

## Documentation updates

Updated:

- `README.md`
- `docs/QUICK_START.md`
- `MARKETPLACE_NOTES.md`
- `SECURITY.md`
- `docs/RELEASE_NOTES_v0.1.0-early-access.md`
- `docs/GITHUB_RELEASE_REPORT.md`
- `docs/LAUNCH_STACK.md`
- `marketplace/SSH_REMEDIATION_REPORT.md`

Documentation now clearly separates:

Marketplace:

- Stable template filename: `template-medium-ami-direct-autonet.yaml`
- EC2 Key Pair required
- SSH reviewer/operational validation supported

GitHub:

- Easy-mode template: `template-medium-ami-direct-autonet-github.yaml`
- EC2 Key Pair optional
- 3-value deployment UX preserved

## Marketplace resubmission readiness

Marketplace SSH split-template remediation is ready for resubmission from the template perspective.

Recommended resubmission note:

- Use the unchanged Marketplace template URL ending in `template-medium-ami-direct-autonet.yaml`.
- Select an EC2 Key Pair during stack launch.
- SSH as `ubuntu` with the matching private key.
- Confirm `PasswordAuthentication no` and key-based authentication.
