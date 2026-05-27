# Final AMI Cleanup Report

## Summary

Final legacy AMI cleanup is complete. Only the latest active public release AMI and its active root snapshot remain. The prior us-east-2 AMI was removed in the follow-up us-east-1-only cleanup.

## Remaining active AMIs

### us-east-1

- AMI: `ami-0a56576d054e9228e`
- Snapshot: `snap-0b73679b56ef084b8`
- State: `available`
- Public: `true`
- Purpose: AWS Marketplace source-region AMI

## Deleted AMIs

### us-east-1

- `ami-071db7a079c2d5b0c`
- `ami-02f7ffd81a3e63a98`
- `ami-0c8d6f2b1c7acdc67`

### us-east-2

- `ami-0bfc554348685c913`
- `ami-015e7593aa33c1200`
- `ami-0111607018603b1cb`

## Deleted snapshots

### us-east-1

- `snap-0e5fc4a3dd3019e56`
- `snap-0514d553e6359f97e`
- `snap-0ed6a777975da2ea5`

### us-east-2

- `snap-0ecb5473ac7987f1f`
- `snap-046c3787978b89a4e`
- `snap-0cce629ae703964d8`

## Estimated storage reduction

Deleted snapshots:

- 6 snapshots
- 256 GiB each
- Estimated deleted EBS snapshot logical volume size: `1,536 GiB`

Actual billable reduction may be lower because EBS snapshots are incremental, but all obsolete root snapshots associated with obsolete Orbs AMIs were deleted.

## Pre-cleanup safety checks

Confirmed before deletion:

- Marketplace public S3 template validates successfully.
- Marketplace public S3 template now references active `us-east-1` AMI `ami-0a56576d054e9228e`.
- GitHub easy-mode public S3 template validates successfully.
- GitHub easy-mode public S3 template now references active `us-east-1` AMI `ami-0a56576d054e9228e`.
- Current-facing README/docs/Marketplace notes were updated to remove obsolete AMI references.
- AutoNet behavior remains intact; public templates still have no customer `VpcId` or `SubnetId` parameters.

## Post-cleanup verification

Remaining Orbs-related AMIs:

```text
us-east-1: ami-0a56576d054e9228e -> snap-0b73679b56ef084b8
```

Public CloudFormation TemplateURL validation passed after cleanup:

```text
Marketplace template ImageId: ami-0a56576d054e9228e
GitHub template ImageId: ami-0a56576d054e9228e
```

Marketplace behavior remains:

- `KeyName` optional with auto-create fallback
- `SshAccessCidr` required / no default `0.0.0.0/0`
- `AccessCidr` secure default `127.0.0.1/32`
- No `VpcId` or `SubnetId` parameters

GitHub behavior remains:

- `KeyName` optional
- 3-value deployment UX preserved
- AutoNet provisioning preserved

## Deployment flow confirmation

Marketplace and GitHub deployment flows remain functional from the template/reference perspective:

- Public S3 templates validate successfully with CloudFormation.
- Both public templates reference an existing, public, available `us-east-2` AMI.
- Marketplace source-region AMI remains available in `us-east-1`.
- Obsolete AMI IDs are no longer required by current public templates or current-facing documentation.
