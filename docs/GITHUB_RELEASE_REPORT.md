# GitHub Release Report

- Brand: KRYP Labs
- Release tag: `v0.1.0-early-access`
- Release title: `KRYP Labs Orbs Boyar AWS Launcher v0.1.0 Early Access`
- Public AMI: `ami-0bfc554348685c913`
- Region: `us-east-2`
- Release prep commit / tag target: `5f0b7e0`
- Latest main after report finalization: see current `origin/main` (report-only follow-up commits may be newer than the release tag)

## Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct-autonet.yaml
```

## Completed actions

- Updated `CHANGELOG.md` for `v0.1.0-early-access`.
- Created `docs/RELEASE_NOTES_v0.1.0-early-access.md`.
- Confirmed README includes the Launch Stack URL, public AMI ID, `us-east-2` support, direct NoEcho private key input, Elastic IP automation/reuse, and AWS cost responsibility.
- Ran public repo safety scan.
- Committed and pushed release prep docs.
- Created and pushed git tag `v0.1.0-early-access`.

## Safety scan

Final public repository safety scan passed:

- no `.env` files
- no private key blocks
- no `.pem` / key-like files
- no AWS access key IDs or secret key assignments
- no pre-signed URLs
- no exact local private key or RPC endpoint values from the build environment

## GitHub Release status

GitHub CLI was not available in the environment, and no GitHub API token was present. A GitHub Release was therefore **not created automatically**.

Manual creation details:

- Tag: `v0.1.0-early-access`
- Target: pushed tag `v0.1.0-early-access`
- Title: `KRYP Labs Orbs Boyar AWS Launcher v0.1.0 Early Access`
- Body file: `docs/RELEASE_NOTES_v0.1.0-early-access.md`

Equivalent command if GitHub CLI is available later:

```bash
gh release create v0.1.0-early-access \
  --repo goldenman-kr/orbs-boyar-aws-launcher \
  --title "KRYP Labs Orbs Boyar AWS Launcher v0.1.0 Early Access" \
  --notes-file docs/RELEASE_NOTES_v0.1.0-early-access.md
```

## Known limitations

- Region support is `us-east-2` only.
- This is a GitHub early-access release, not an AWS Marketplace listing.
- Direct private key input uses CloudFormation `NoEcho`; this hides values from UI/API outputs but is not a dedicated secret store.
- Users pay their own AWS infrastructure costs.
- Default network access exposes `22`, `80`, and `7666` to `0.0.0.0/0` unless users restrict `AccessCidr`.
- HTTPS/TLS, domain setup, monitoring, alerting, backups, and upgrade automation are not included.
