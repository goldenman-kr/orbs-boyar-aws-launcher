# Sanitized Release Transition Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Sanitized release AMI: `ami-0bfc554348685c913`
- Sanitized release snapshot: `snap-0ecb5473ac7987f1f`
- Public template delivery: S3 HTTPS TemplateURL
- CloudFormation stacks deployed during this phase: none
- AMI visibility changed during this phase: no
- AMI/snapshot deleted during this phase: no
- Marketplace submission attempted: no

## Summary

The public Launch Stack artifacts were updated to reference the sanitized release-candidate AMI:

```text
ami-0bfc554348685c913
```

The AMI is still private at the time of this transition. The system is prepared for the next explicit approval step: making this exact AMI public.

## Updated artifacts

- `cloudformation/template-medium-ami-secrets.yaml`
- `README.md`
- `docs/QUICK_START.md`
- `docs/LAUNCH_STACK.md`
- `MARKETPLACE_NOTES.md`
- AMI/snapshot references in other public docs

## Public template delivery object

The updated CloudFormation template was uploaded to the existing public S3 TemplateURL object:

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-secrets.yaml
```

Object settings:

- Content-Type: `application/x-yaml`
- Cache-Control: `no-cache`
- Public readability preserved by bucket policy

## Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-secrets.yaml
```

## Validation

Validation performed without creating a CloudFormation stack:

- S3 object public HEAD request returned HTTP 200
- `aws cloudformation validate-template --template-url` accepted the S3 TemplateURL
- CloudFormation validation returned the expected parameters including `ImageId`
- Uploaded S3 template contains `ami-0bfc554348685c913`
- Public-doc consistency scan passed

## Remaining step before public testing

Make this exact AMI public after explicit approval:

```text
ami-0bfc554348685c913
```

After visibility is changed, verify public launch permissions and then perform a clean Launch Stack test.

## Blockers

No documentation/template blockers remain for the public AMI visibility change.

Operational reminder: do not delete older AMIs/snapshots until the sanitized AMI is publicly verified and explicit cleanup approval is given.
