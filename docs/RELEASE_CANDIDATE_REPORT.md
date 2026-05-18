# Release Candidate Report

- Package: KRYP Labs Orbs Boyar AWS Launcher
- Release candidate: `0.1.0-rc.1`
- Created: 2026-05-18
- Region support: `us-east-2` only
- AMI ID: `ami-06d0ede0e331a96d2`
- Snapshot: `snap-0039ee6a089ecb6a2`
- Final preferred template: `cloudformation/template-medium-ami-secrets.yaml`
- AWS resources created during packaging: none
- AMI/snapshot changes during packaging: none
- Marketplace submission attempted: no

## Files included

```text
orbs-boyar-aws-launcher/
├── ARCHITECTURE.md
├── CHANGELOG.md
├── MARKETPLACE_NOTES.md
├── README.md
├── RELEASE_CANDIDATE_REPORT.md
├── SECURITY.md
├── TROUBLESHOOTING.md
├── cloudformation/
│   └── template-medium-ami-secrets.yaml
├── examples/
│   └── .env.template
└── public-release-safety-scan.log
```

Included artifacts are public-facing or placeholder-only:

- Secrets Manager hardened Medium AMI CloudFormation template
- English canonical README
- Security notes
- Troubleshooting guide
- Marketplace planning notes
- Architecture summary
- Changelog
- Placeholder `.env.template`
- Safety scan log

## Files excluded

The release candidate intentionally excludes:

- Real `.env` files
- Private keys
- Local `.pem` files
- AWS credentials
- CloudFormation deployment logs
- Temporary deploy reports
- Pre-signed URLs
- `archive-delivery.env`
- Test stack parameter files
- Test stack outputs with public IPs
- Raw installer archives
- AMI build/test logs
- Any file containing the exact local private key or exact local Ethereum endpoint value

## Safety scan result

Public-release safety scan passed.

Evidence:

```text
orbs-boyar-aws-launcher/public-release-safety-scan.log
```

Checks performed:

- No exact private key value
- No exact local Ethereum endpoint value
- No real `.env` file
- No AWS credential pattern
- No pre-signed URL pattern
- No local `.pem` or `.key` file
- No `archive-delivery.env`
- No private key block
- No raw `PRIVATE_KEY_NO_LEADING_0x=<64 hex>` assignment
- No unapproved 64-hex secret-like literal

## Template note

The release-candidate copy of the validated Secrets Manager template is public-facing and uses the KRYP Labs launcher project tag value:

```text
orbs-boyar-aws-launcher
```

The runtime behavior remains the validated Secrets Manager Medium AMI flow:

1. Launch EC2 from Medium AMI `ami-06d0ede0e331a96d2`.
2. Use EC2 IAM Role to read only the specified `PrivateKeySecretArn`.
3. Write `/opt/orbs/ami_creator/.env` with mode `0600`.
4. Run `install.sh --configure-and-start`.

## Remaining tasks before public release

- Confirm AWS Marketplace source-region requirements.
- Decide whether to rebuild/copy AMI for Marketplace source-region needs.
- Run AWS Marketplace AMI scan and remediate findings.
- Finalize seller account and listing identity for KRYP Labs.
- Prepare final legal/license/support documentation.
- Review whether root EBS encryption should be enabled by default.
- Review public exposure of status ports `80` and `7666`.
- Review whether Secrets Manager secret creation should be included in a helper guide or nested stack.
- Replace any release-candidate wording with final product wording.
- Conduct a final clean-room launch test from the release directory.
- Prepare GitHub repository layout and release tag.
- Prepare Marketplace listing title, screenshots, long description, usage instructions, and support contacts.

## AMI/snapshot retention recommendation

Keep the AMI and snapshot retained for the next-stage work.

Reason:

- The AMI passed direct Medium AMI validation.
- The AMI passed CloudFormation Medium AMI launch validation.
- The AMI passed Secrets Manager hardened CloudFormation launch validation.
- The release-candidate package references this AMI.

Retained resources:

```text
AMI: ami-06d0ede0e331a96d2
Snapshot: snap-0039ee6a089ecb6a2
```

Do not delete the AMI/snapshot until the next-stage packaging/Marketplace decision is complete or Eddy explicitly approves deletion.
