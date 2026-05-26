# Marketplace SSH Remediation Report

> Superseded split-template update: the stable Marketplace template `template-medium-ami-direct-autonet.yaml` now requires `KeyName`; the separate GitHub easy-mode template `template-medium-ami-direct-autonet-github.yaml` preserves optional SSH and the 3-value UX.

## Summary

AWS Marketplace reviewer SSH accessibility feedback has been remediated in the AutoNet CloudFormation template.

Reviewer feedback addressed:

- SSH service must be running and accessible on TCP/22.
- `PasswordAuthentication no` must be explicitly configured.
- Reviewer must be able to SSH into the instance with key-based authentication.

## Final AMI IDs

No new AMIs were required for this remediation. The fix is implemented in CloudFormation first-boot behavior and Marketplace launch defaults.

- `us-east-2`: `ami-0111607018603b1cb`
- `us-east-1`: `ami-071db7a079c2d5b0c`

## Template updated

Updated template:

- `cloudformation/template-medium-ami-direct-autonet.yaml`

### SSH ingress behavior

A dedicated SSH CIDR parameter was added:

- `SshAccessCidr`
- Default: `0.0.0.0/0`
- Scope: TCP/22 only

The existing `AccessCidr` now controls only node/status endpoints:

- TCP/80
- TCP/7666
- Default remains secure-by-default: `127.0.0.1/32`

This now pairs with the split-template approach: the Marketplace template requires `KeyName` for AWS Marketplace SSH validation, while the GitHub easy-mode template preserves the 3-value default UX and optional SSH.

### SSH KeyPair behavior after split

Marketplace template `template-medium-ami-direct-autonet.yaml` requires `KeyName` so reviewers can authenticate with the matching private key. GitHub template `template-medium-ami-direct-autonet-github.yaml` preserves optional `KeyName`: empty launches without SSH key login, provided value injects the selected public key and populates `SSHCommand`.

## First-boot SSH hardening

UserData now explicitly ensures SSH is enabled, running, and key-only before application configuration:

```text
systemctl unmask ssh.service ssh.socket
systemctl enable ssh.service
/etc/ssh/sshd_config.d/99-orbs-marketplace-key-only.conf
sshd -t
systemctl restart ssh.service
systemctl is-active ssh.service
```

The generated SSH drop-in contains:

```text
PasswordAuthentication no
KbdInteractiveAuthentication no
ChallengeResponseAuthentication no
PubkeyAuthentication yes
PermitRootLogin prohibit-password
```

## Validation results

Validation stack:

- Region: `us-east-2`
- AMI: `ami-0111607018603b1cb`
- Instance: `i-059b75669f9676d40`
- Final Elastic IP used for SSH validation: `18.226.10.192`
- KeyName: `ssh-remediation-20260526234946-key`

Observed results:

```text
TCP/22 reachability: tcp22-open
SSH key login: ssh-key-login-ok
sshd/systemd status: active
permitrootlogin without-password
pubkeyauthentication yes
passwordauthentication no
kbdinteractiveauthentication no
Password login attempt exit code: 255
Password login result: rejected
```

## CloudFormation validation

`aws cloudformation validate-template` passed for:

- `cloudformation/template-medium-ami-direct-autonet.yaml`

Key rendered defaults:

```text
ImageId: ami-0111607018603b1cb
SshAccessCidr: 0.0.0.0/0
AccessCidr: 127.0.0.1/32
Marketplace KeyName: required; GitHub KeyName: optional empty string
```

## Documentation updates

Updated documentation now states:

- Marketplace SSH validation is supported by default through `SshAccessCidr=0.0.0.0/0`.
- SSH remains key-only and password authentication is explicitly disabled at first boot.
- Production users should restrict `SshAccessCidr` to trusted operator IP/CIDR.
- Node/status endpoints remain secure-by-default through `AccessCidr=127.0.0.1/32`.
- Marketplace `KeyName` is required for reviewer SSH validation; GitHub easy-mode `KeyName` remains optional for normal 3-value launches.

## Recommendation for Marketplace resubmission

Resubmit with the updated AutoNet template and the existing public AMIs.

Marketplace reviewer instructions should include:

1. Launch the AutoNet CloudFormation template.
2. Provide the three required Orbs runtime values.
3. Select an EC2 Key Pair in `KeyName` for SSH validation; this parameter is required by the Marketplace template.
4. Leave `SshAccessCidr` at the Marketplace validation default or set it to the reviewer-controlled CIDR.
5. SSH as `ubuntu` using the matching private key.
6. Confirm password authentication is disabled.
