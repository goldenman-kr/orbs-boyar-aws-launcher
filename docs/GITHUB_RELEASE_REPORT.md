# GitHub Release Report

- Brand: KRYP Labs
- Release: `v0.1.0-early-access`
- Public AMI: `ami-0bfc554348685c913`
- Region: `us-east-2`
- Launch Stack URL:

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Summary

Prepared the repository for the public early-access GitHub release.

## Release notes

Release notes file:

```text
docs/RELEASE_NOTES_v0.1.0-early-access.md
```

## Safety scan

Pending final safety scan.

## Git status

Pending final commit/tag/release creation.

## GitHub Release

Pending.

## Known limitations

- Region support is `us-east-2` only.
- This is a GitHub early-access release, not an AWS Marketplace listing.
- Direct private key input uses CloudFormation `NoEcho`; this hides values from UI/API outputs but is not a dedicated secret store.
- Users pay their own AWS infrastructure costs.
- Default network access exposes `22`, `80`, and `7666` to `0.0.0.0/0` unless users restrict `AccessCidr`.
- HTTPS/TLS, domain setup, monitoring, alerting, backups, and upgrade automation are not included.
