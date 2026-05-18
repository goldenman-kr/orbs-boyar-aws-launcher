# us-east-1 Marketplace AMI Report

- Brand: KRYP Labs
- Source region: `us-east-2`
- Source AMI: `ami-0bfc554348685c913`
- Source snapshot: `snap-0ecb5473ac7987f1f`
- Target region: `us-east-1`
- Marketplace source AMI: `ami-0c8d6f2b1c7acdc67`
- Marketplace source snapshot: `snap-0ed6a777975da2ea5`
- AMI rebuild performed: no
- AMI copy mechanism: AWS EC2 `copy-image`
- CloudFormation stacks deployed: no
- Marketplace submission attempted: no

## Summary

The public sanitized release AMI was copied from `us-east-2` to `us-east-1` using AWS AMI copy functionality for AWS Marketplace source-region preparation.

The existing `us-east-2` public AMI and snapshot were not modified.

## Source artifact verification

Before copy, the source AMI was verified:

```text
AMI: ami-0bfc554348685c913
Region: us-east-2
State: available
Public: true
Snapshot: snap-0ecb5473ac7987f1f
Snapshot state: completed
Encrypted: false
Volume size: 256 GiB
```

## Copied artifact

```text
AMI: ami-0c8d6f2b1c7acdc67
Region: us-east-1
State: available
Public: true
Snapshot: snap-0ed6a777975da2ea5
Snapshot state: completed
Encrypted: false
Volume size: 256 GiB
```

## Public visibility verification

Verified after copy:

- AMI visibility: public
- AMI launch permissions: `Group=all`
- Snapshot create volume permissions: `Group=all`
- AMI state: `available`
- Snapshot state: `completed`

## Current Launch Stack status

The public GitHub Launch Stack remains `us-east-2` only and continues to use:

```text
ami-0bfc554348685c913
```

Current Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

The `us-east-1` AMI is prepared for Marketplace registration/source-region review, not for the current public Launch Stack flow.

## Marketplace readiness assessment

The `us-east-1` AMI is now ready for Marketplace registration preparation from an AMI availability/visibility perspective.

Remaining Marketplace blockers still include:

- Confirm AWS Marketplace source-region and seller-account requirements.
- Run AWS Marketplace AMI scanning against `ami-0c8d6f2b1c7acdc67`.
- Confirm seller onboarding, support contact, legal/EULA, and product metadata.
- Review public SSH/default network exposure.
- Review direct-input private key mode vs Secrets Manager mode as the primary Marketplace path.
- Review root EBS encryption posture.
- Prepare screenshots/assets and perform a private Marketplace test listing.
