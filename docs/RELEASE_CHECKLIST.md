# Release Checklist

## Before tagging a public release

- [ ] Confirm the release package contains only public-safe files.
- [ ] Run the public repo safety scan.
- [ ] Validate the CloudFormation template in `us-east-2`.
- [ ] Perform a clean-room launch test from this repository directory.
- [ ] Confirm AMI `ami-0bfc554348685c913` is still available in `us-east-2`.
- [ ] Confirm snapshot `snap-0ecb5473ac7987f1f` is intentionally retained.
- [ ] Review `README.md`, `SECURITY.md`, and `TROUBLESHOOTING.md` for public wording.
- [ ] Confirm KRYP Labs branding and support contact details.
- [ ] Confirm license and Marketplace notes.

## Before AWS Marketplace submission

- [ ] Confirm Marketplace source-region requirements.
- [ ] Run AWS Marketplace AMI scanning.
- [ ] Review root volume encryption defaults.
- [ ] Review public endpoint exposure model.
- [ ] Prepare legal, support, seller, pricing, and listing assets.
- [ ] Perform private limited Marketplace testing.

## Never include

- [ ] Real `.env` files
- [ ] Private keys
- [ ] PEM/key files
- [ ] AWS credentials
- [ ] Pre-signed URLs
- [ ] Deployment logs with IPs or sensitive values
- [ ] Temporary test reports that may contain account-specific details
