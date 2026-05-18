# AWS Marketplace Notes

## Strategy

Target distribution: free AWS Marketplace AMI-based product plus GitHub Launch Stack option.

Branding candidate:

```text
KRYP Labs
```

The software listing should be free. Users still pay their own AWS infrastructure costs.

## AMI-based product notes

Current validated AMI:

```text
Region: us-east-2
AMI ID: ami-0bfc554348685c913
Snapshot: snap-0ecb5473ac7987f1f
```

The AMI is a Medium AMI:

- Stable dependencies and installer files are baked in.
- Secrets and validator-specific runtime config are not baked in.
- Runtime configuration happens at first boot through CloudFormation and AWS Secrets Manager.

## Required future AWS Marketplace steps

Before public listing:

1. Confirm Marketplace source-region requirements.
2. Decide whether to rebuild or copy the AMI to the required Marketplace source region.
3. Run AWS Marketplace AMI scanning and hardening checks.
4. Review root volume encryption requirements.
5. Review public Security Group defaults and exposed status ports.
6. Replace release-candidate/test tag names with final product tags if needed.
7. Prepare public product title, short description, long description, usage instructions, support contacts, EULA/license, and refund/pricing details.
8. Confirm KRYP Labs seller account, tax, banking, and support profile readiness.
9. Create Marketplace product draft.
10. Perform private limited test before public release.

## Remaining blockers before public listing

- AMI currently available only in `us-east-2`.
- Marketplace source-region and publication flow not yet confirmed.
- No Marketplace scan has been run yet.
- No final legal/license/support package is included.
- Template has not been reviewed by an AWS Marketplace specialist.
- Root volume encryption default should be reviewed.
- Public endpoint exposure model should be reviewed.
- Monitoring, alerting, TLS/domain setup, and upgrade policy are not yet packaged.

## Do not do yet

- Do not submit to Marketplace.
- Do not copy/share the AMI to another account.
- Do not create a new AMI unless explicitly approved.
- Do not delete the current AMI or snapshot while next-stage work is active.
