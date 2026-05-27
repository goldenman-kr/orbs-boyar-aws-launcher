# us-east-1 Only AMI Cleanup Report

## Summary

The Orbs Boyar AWS Launcher distribution has been converted to `us-east-1` only.

The remaining active public AMI is:

- `us-east-1`: `ami-0a56576d054e9228e`
- active snapshot: `snap-0b73679b56ef084b8`

The previous `us-east-2` AMI and snapshot were deregistered/deleted:

- deleted AMI: `ami-09399cf1f338175fc`
- deleted snapshot: `snap-04f014749b85ff413`

## Public template state

The public S3 templates were updated without changing their URLs. All now reference only the `us-east-1` active AMI `ami-0a56576d054e9228e`:

- `template-medium-ami-direct-autonet.yaml`
- `template-medium-ami-direct-autonet-github.yaml`
- `template-medium-ami-direct-autonet-github-us-east-1.yaml`

CloudFormation validation passed in `us-east-1` for all three public TemplateURLs.

## GitHub README state

The README now presents only the `us-east-1` Launch Stack button/link, using the `us-east-1` GitHub easy-mode template.

The `us-east-2` Launch Stack button/link and active AMI references were removed from the current README.

## AWS cleanup verification

Post-cleanup checks show no remaining self-owned Orbs-related AMIs in `us-east-2` and no remaining related snapshots in `us-east-2`.

The active `us-east-1` AMI remains public and available:

```text
ami-0a56576d054e9228e -> snap-0b73679b56ef084b8
```

## Functional confirmation

- GitHub deployment flow now targets `us-east-1`.
- Marketplace template now targets `us-east-1` AMI `ami-0a56576d054e9228e`.
- GitHub `KeyName` remains optional.
- Marketplace `KeyName` is optional with existing-key reuse or stack-managed auto-creation.
- AutoNet behavior remains unchanged.
