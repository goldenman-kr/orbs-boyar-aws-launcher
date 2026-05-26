# Marketplace Template Snapshot Refresh Remediation Report

## Summary

The public Marketplace AutoNet CloudFormation template has the intended SSH remediation, but the AWS Marketplace-generated `awsmp-cft` snapshot URL is still serving an older ingested copy.

Conclusion: **Marketplace has cached an old template snapshot.** The public seller-owned S3 TemplateURL is correct; the Marketplace delivery option needs to be re-saved/re-ingested so AWS Marketplace regenerates its internal `awsmp-cft` artifact.

## URLs checked

Current intended Marketplace public TemplateURL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

Marketplace-generated `awsmp-cft` snapshot URL checked:

```text
https://awsmp-cft-053155443450-1579814207723.s3.us-east-1.amazonaws.com/064f61b1-e5bd-409d-a8c1-d9cd3914d5e8/064f61b1-e5bd-409d-a8c1-d9cd3914d5e8/template-medium-ami-direct-autonet.yaml
```

## Public S3 template status

The public seller-owned S3 template is updated correctly.

Observed:

```text
KeyName:
  Type: AWS::EC2::KeyPair::KeyName
  Description: EC2 Key Pair required for SSH access and AWS Marketplace validation.
```

Confirmed:

- `KeyName` no longer has `Default: ''`.
- `KeyName` is required.
- `HasKeyName` optional-SSH conditional is absent.
- EC2 instance uses `KeyName: !Ref KeyName`.
- `VpcId` parameter is absent.
- `SubnetId` parameter is absent.
- AutoNet resources remain present:
  - `OrbsBoyarVpc`
  - `OrbsBoyarPublicSubnet`
  - `OrbsBoyarInternetGateway`
  - `OrbsBoyarDefaultRoute`
  - `OrbsBoyarElasticIp`
- Public TemplateURL validation passes with `aws cloudformation validate-template`.

## Marketplace `awsmp-cft` snapshot status

The Marketplace-generated snapshot is still old.

Observed in the snapshot:

```text
KeyName:
  Type: String
  Default: ''
  Description: Optional existing EC2 Key Pair name for SSH access. Leave empty to launch without an SSH key pair.
```

Confirmed snapshot mismatch:

- `KeyName` still has `Default: ''`.
- `HasKeyName` optional-SSH conditional is still present.
- EC2 instance still uses optional `KeyName` conditional behavior.
- `SshAccessCidr` is absent in the old snapshot.
- SSH ingress in the old snapshot is still tied to `AccessCidr`.
- First-boot SSH hardening block is absent in the old snapshot.
- AutoNet remains present and `VpcId`/`SubnetId` parameters are absent, but the SSH remediation is not reflected.

## Minimal Marketplace action required

A new AMI is **not** required.

A product identity change is **not** required.

The minimum action should be one of the following in AWS Marketplace Management Portal, in this order:

1. Edit the existing CloudFormation delivery option.
2. Re-save the same TemplateURL:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct-autonet.yaml
```

3. If the portal exposes version metadata/release notes for the same version, update/re-save that metadata so Marketplace re-ingests the template artifact.
4. Submit the updated delivery option for review.

If Marketplace does not regenerate the `awsmp-cft` snapshot from the existing version/delivery option, then create a new product version only as a fallback. Keep the same AMI alias/AMI IDs and same product identity.

## Whether a harmless metadata-only template revision is needed

Not required yet.

The public S3 template already differs from the stale Marketplace snapshot and has a valid newer artifact. The blocker is Marketplace ingestion, not public S3 content.

If AWS Marketplace support or the portal requires a visible template change to trigger re-ingestion, make a metadata-only change such as a Description suffix/comment-equivalent metadata field, preserve all resources/parameters/AMI IDs, re-upload the same filename, and re-save the delivery option.

## New version requirement

A new Marketplace version should **not** be necessary if the delivery option can be edited/re-saved and resubmitted.

A new version may be unavoidable only if Marketplace does not allow refreshing the existing delivery option snapshot.

## Current reflected state

- Public seller-owned S3 TemplateURL: **updated / correct**
- Marketplace `awsmp-cft` snapshot URL: **old / not refreshed**
- Marketplace KeyName required reflected in public S3: **yes**
- Marketplace KeyName required reflected in `awsmp-cft` snapshot: **no**

## Recommendation

Proceed in Marketplace Management Portal with a delivery option refresh/re-save using the unchanged public TemplateURL. Then re-check the generated `awsmp-cft` URL. Resubmission should wait until the generated snapshot shows:

```text
KeyName:
  Type: AWS::EC2::KeyPair::KeyName
  Description: EC2 Key Pair required for SSH access and AWS Marketplace validation.
```

and no longer contains:

```text
Default: ''
```
