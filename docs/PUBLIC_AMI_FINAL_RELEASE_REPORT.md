# Public AMI Final Release Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Target AMI: `ami-0bfc554348685c913`
- Target snapshot: `snap-0ecb5473ac7987f1f`
- Result: **Blocked — AMI was not made public**
- CloudFormation stacks deployed: no
- New AMIs created: no
- AMI/snapshot deleted: no
- Marketplace submission attempted: no

## Summary

The final public AMI release was attempted after preflight verification passed, but AWS rejected the AMI public launch-permission change because account-level **AMI Block Public Access** is enabled.

The AMI is still private:

```text
AMI: ami-0bfc554348685c913
Public: false
State: available
```

The backing snapshot public permission was briefly added as part of the publication sequence, but because AMI publication failed, that snapshot public permission was immediately removed. The snapshot is not public now.

## Preflight verification

Passed before any visibility change was attempted:

- AMI exists
- AMI state is `available`
- AMI was private before release
- AMI launch permissions were not public before release
- Snapshot exists
- Snapshot state is `completed`
- Snapshot is unencrypted and backs the AMI root volume

## Blocking AWS error

AWS rejected public AMI sharing with:

```text
OperationNotPermitted: You can’t publicly share this image because block public access for AMIs is enabled for this account. To publicly share the image, you must call the DisableImageBlockPublicAccess API.
```

This is an account-level public AMI sharing safeguard. It was not disabled in this phase.

## Post-failure cleanup verification

After the failed publication attempt:

- AMI launch permissions: not public
- AMI `Public`: `false`
- Snapshot `createVolumePermission`: empty / not public
- Snapshot contents were not modified
- Old AMIs/snapshots were not deleted

## Current release status

The GitHub Launch Stack template already points to the sanitized AMI ID, but the AMI remains private. External users cannot launch it until AMI Block Public Access is intentionally disabled and the AMI is shared publicly.

Current public template URL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

Target AMI to make public after approval:

```text
ami-0bfc554348685c913
```

## Remaining risks / blockers

- Account-level AMI Block Public Access prevents public AMI sharing.
- AMI visibility still needs to be changed after explicit approval to disable or otherwise handle the account-level safeguard.
- A clean external-account Launch Stack test has not yet been run because the AMI is not public.

## Recommendations

### Public AMI release next step

Explicitly decide whether to disable AMI Block Public Access for this AWS account/region long enough to publish the sanitized AMI, then re-enable the safeguard if appropriate for account policy.

After that approval:

1. Make snapshot `snap-0ecb5473ac7987f1f` publicly readable if required for the public AMI.
2. Add public launch permission to AMI `ami-0bfc554348685c913`.
3. Verify public AMI visibility and launch permissions.
4. Run a clean Launch Stack test from an external or unaffiliated AWS account.

### Old AMI cleanup

Do not delete old AMIs/snapshots yet. After the sanitized AMI is public and externally verified, request explicit cleanup approval for:

- Previous test AMI: `ami-06d0ede0e331a96d2`
- Previous test snapshot: `snap-0039ee6a089ecb6a2`
- Intermediate failed sanitized AMI: `ami-016b1be091f5a9278`
- Intermediate failed sanitized snapshot: `snap-0537161ad7b32a47c`

### Marketplace next steps

- Confirm AWS Marketplace source-region requirements.
- Run AWS Marketplace AMI scanning on the sanitized AMI.
- Review AMI Block Public Access policy impact on Marketplace publication flow.
- Prepare legal/support/listing assets under KRYP Labs.
