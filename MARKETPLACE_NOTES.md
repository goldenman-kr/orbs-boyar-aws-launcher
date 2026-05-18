# AWS Marketplace Notes

## Strategy

Target distribution: free AWS Marketplace AMI-based product plus GitHub Launch Stack option.

Branding candidate:

```text
KRYP Labs
```

The software listing should be free. Users still pay their own AWS infrastructure costs.

## AMI-based product notes

Current public early-access AMI:

```text
Region: us-east-2
AMI ID: ami-0bfc554348685c913
Snapshot: snap-0ecb5473ac7987f1f
AMI visibility: public in us-east-2 for early-access GitHub Launch Stack testing
```

Marketplace source-region AMI copy:

```text
Region: us-east-1
AMI ID: ami-0c8d6f2b1c7acdc67
Snapshot: snap-0ed6a777975da2ea5
AMI visibility: public in us-east-1 for Marketplace source-region preparation
```

The AMI is a Medium AMI:

- Stable dependencies and installer files are baked in.
- Secrets and validator-specific runtime config are not baked in.
- Runtime configuration happens at first boot through CloudFormation. Early-access users can use direct NoEcho private key input; higher-security production and Marketplace flows should prefer the retained Secrets Manager template.

## Launch templates

- `cloudformation/template-medium-ami-direct.yaml`: simpler early-access template. It accepts `PrivateKeyNoLeading0x` directly as a CloudFormation `NoEcho` parameter. This improves public testing usability, but the private key still passes through CloudFormation parameter handling during stack creation.
- `cloudformation/template-medium-ami-secrets.yaml`: retained for higher-security production and future Marketplace use. Users create an AWS Secrets Manager secret and pass only `PrivateKeySecretArn`.

Recommended posture: use direct-input mode for early-access testers who need the simplest Launch Stack path; use Secrets Manager mode for Marketplace/public production packaging. The direct-input template automatically allocates an Elastic IP by default and now supports optional `ExistingEipAllocationId` reuse for reinstall/recovery. Auto-created EIPs are released on stack deletion; reused EIPs remain user-managed and may incur charges. The Secrets Manager template keeps automatic EIP behavior for higher-security testing.

## Required future AWS Marketplace steps

Before public listing:

1. Confirm Marketplace source-region requirements and whether `us-east-1` is accepted for this listing.
2. Use prepared `us-east-1` AMI `ami-0c8d6f2b1c7acdc67` for Marketplace source-region review if accepted.
3. Run AWS Marketplace AMI scanning and hardening checks.
4. Review root volume encryption requirements.
5. Review Security Group defaults, optional public access guidance, exposed status ports, and Elastic IP lifecycle/reuse behavior.
6. Replace release-candidate/test tag names with final product tags if needed.
7. Prepare public product title, short description, long description, usage instructions, support contacts, EULA/license, and refund/pricing details.
8. Confirm KRYP Labs seller account, tax, banking, and support profile readiness.
9. Create Marketplace product draft.
10. Perform private limited test before public release.

## Remaining blockers before public listing

- GitHub Launch Stack is still `us-east-2` only; Marketplace source AMI is now prepared in `us-east-1` as `ami-0c8d6f2b1c7acdc67`.
- Marketplace source-region and publication flow not yet confirmed.
- No Marketplace scan has been run yet.
- No final legal/license/support package is included.
- Template has not been reviewed by an AWS Marketplace specialist.
- Root volume encryption default should be reviewed.
- Optional public endpoint exposure and Elastic IP lifecycle/reuse model should be reviewed.
- Monitoring, alerting, TLS/domain setup, and upgrade policy are not yet packaged.

## Do not do yet

- Do not submit to Marketplace.
- Do not copy/share the AMI to another account.
- Do not create a new AMI unless explicitly approved.
- Do not delete the current AMI or snapshot while next-stage work is active.


## Marketplace ingress remediation

The direct-input Marketplace-oriented template now defaults `AccessCidr` to `127.0.0.1/32` instead of `0.0.0.0/0`. Users can still explicitly enter their own `x.x.x.x/32`, another trusted CIDR range, or `0.0.0.0/0` if they intentionally want public access. This preserves functionality while using secure defaults for Marketplace review.
