# Marketplace Review Remediation Report 001 — Secure Default Ingress CIDR

- Brand: KRYP Labs
- Reviewer concern: default ingress CIDR should not be `0.0.0.0/0`
- Template updated: `cloudformation/template-medium-ami-direct-autonet.yaml`
- Public AMI: `ami-0bfc554348685c913`
- Region for current Launch Stack: `us-east-2`

## Summary

The direct-input Marketplace-oriented CloudFormation template now uses a secure default ingress CIDR.

`AccessCidr` default changed from:

```text
0.0.0.0/0
```

to:

```text
127.0.0.1/32
```

Users can still explicitly choose public access by entering `0.0.0.0/0`, but public ingress is no longer the default.

## Updated parameter description

```text
CIDR allowed to access exposed endpoints, including SSH on port 22 and Boyar/node endpoints on ports 80 and 7666. Use x.x.x.x/32 to allow a single trusted IP, 0.0.0.0/0 to allow all IPs, or another CIDR range you control.
```

## Functionality preserved

- Users may still manually enter `0.0.0.0/0` if they intentionally want public access.
- Users may enter `x.x.x.x/32` to allow one trusted IP.
- Users may enter another CIDR range they control.
- Elastic IP auto-create/reuse behavior is unchanged.
- Direct private key input with `PrivateKeyNoLeading0x` and `NoEcho: true` is unchanged.

## Documentation updated

- `README.md`
- `docs/QUICK_START.md`
- `SECURITY.md`
- `MARKETPLACE_NOTES.md`

The documentation now explains that public access is optional and user-controlled, and that the Marketplace template uses secure ingress defaults.

## Validation

- Local `aws cloudformation validate-template` passed.
- Public S3 TemplateURL was updated.
- `aws cloudformation validate-template --template-url` passed.
- Launch Stack parameter rendering shows `AccessCidr` default `127.0.0.1/32` and the updated description.
- Public S3 template body contains `Default: 127.0.0.1/32`.

## Marketplace blocker assessment

This should address the specific reviewer feedback: the template no longer defaults ingress to `0.0.0.0/0`, while still allowing users to opt into public access explicitly if their operational model requires it.
