# AMI Cleanup Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Cleanup date: 2026-05-18
- Preserved public release AMI: `ami-0bfc554348685c913`
- Preserved public release snapshot: `snap-0ecb5473ac7987f1f`
- New AMI created: no
- CloudFormation stack deployed: no
- Marketplace submission attempted: no

## Summary

Obsolete pre-release AMIs and their backing snapshots were removed after confirming that the public Launch Stack templates and repository docs no longer depend on them.

The current public release AMI and snapshot were not modified or deleted.

## Deleted AMIs

- `ami-06d0ede0e331a96d2`
- `ami-016b1be091f5a9278`

Both obsolete AMIs were private before cleanup and are no longer returned by `describe-images`.

## Deleted snapshots

- `snap-0039ee6a089ecb6a2`
- `snap-0537161ad7b32a47c`

Both obsolete snapshots are no longer returned by `describe-snapshots`.

## Preserved release artifacts

The active public release AMI remains available and public:

```text
AMI: ami-0bfc554348685c913
State: available
Public: true
Backing snapshot: snap-0ecb5473ac7987f1f
```

The active public release snapshot remains available:

```text
Snapshot: snap-0ecb5473ac7987f1f
State: completed
Volume size: 256 GiB
```

## Reference validation

Before cleanup, the public S3 Launch Stack templates were checked and referenced only the current public AMI.

After cleanup:

- No delete-candidate AMI/snapshot IDs remain in repository docs/templates, except this historical cleanup report.
- Public S3 direct-input template remains clean.
- Public S3 Secrets Manager template remains clean.
- Current public release flow still points to `ami-0bfc554348685c913`.

## Remaining self-owned Orbs Boyar AMIs

Only the current public release AMI remains among self-owned `orbs-boyar-*` images:

```text
ami-0bfc554348685c913 / orbs-boyar-medium-rc1-sanitized-20260518202455
```

## Estimated cleanup effect

- Removes two obsolete private AMI registrations.
- Removes two obsolete 256 GiB snapshots.
- Reduces snapshot storage cost exposure by approximately 512 GiB-month equivalent going forward, subject to AWS EBS snapshot incremental storage accounting.
- Reduces operator confusion by leaving only the current public release AMI/snapshot active for the launcher.
