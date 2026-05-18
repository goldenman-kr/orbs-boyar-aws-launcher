# GitHub Release Report

- Brand: KRYP Labs
- Release tag: \
- Release title: \
- Public AMI: \
- Region: \
- Release prep commit: \
- Tag target: \

## Launch Stack URL

\\\

## Completed actions

- Updated \ for \.
- Created \.
- Confirmed README includes the Launch Stack URL, public AMI ID, \ support, direct NoEcho private key input, Elastic IP automation/reuse, and AWS cost responsibility.
- Ran public repo safety scan.
- Committed and pushed release prep docs.
- Created and pushed git tag \.

## Safety scan

Final public repository safety scan passed:

- no \ files
- no private key blocks
- no \ / key-like files
- no AWS access key IDs or secret key assignments
- no pre-signed URLs
- no exact local private key or RPC endpoint values from the build environment

## GitHub Release status

GitHub CLI was not available in the environment, and no GitHub API token was present. A GitHub Release was therefore **not created automatically**.

Manual creation details:

- Tag: \
- Target: pushed tag \
- Title: \
- Body file: \

Equivalent command if GitHub CLI is available later:

\\\

## Known limitations

- Region support is \ only.
- This is a GitHub early-access release, not an AWS Marketplace listing.
- Direct private key input uses CloudFormation \; this hides values from UI/API outputs but is not a dedicated secret store.
- Users pay their own AWS infrastructure costs.
- Default network access exposes \, \, and \ to \ unless users restrict \.
- HTTPS/TLS, domain setup, monitoring, alerting, backups, and upgrade automation are not included.
