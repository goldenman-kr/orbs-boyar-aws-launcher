# Elastic IP Reuse Mode Report

- Brand: KRYP Labs
- Region: `us-east-2`
- Public AMI: `ami-0bfc554348685c913`
- Updated template: `cloudformation/template-medium-ami-direct.yaml`
- Marketplace submission attempted: no
- New AMI created: no
- Existing AMI/snapshot modified or deleted: no

## Summary

The direct-input Launch Stack template now preserves beginner-friendly automatic Elastic IP behavior while adding optional reuse support for advanced reinstall/recovery workflows.

Default users can ignore `ExistingEipAllocationId`. The stack creates a new EIP automatically, associates it with the validator instance, and releases it when the stack is deleted.

Advanced users can provide an existing EIP AllocationId. The stack associates that existing EIP to the validator instance and does not release it when the stack is deleted, allowing the same public node IP to survive reinstalls.

## Template behavior

Parameter added:

```text
ExistingEipAllocationId
```

Description:

```text
Existing Elastic IP AllocationId to reuse during reinstall or recovery. Leave empty to create a new EIP automatically.
```

CloudFormation conditions:

- `CreateNewEip`: true when `ExistingEipAllocationId` is empty
- `UseExistingEip`: true when `ExistingEipAllocationId` is provided

Behavior:

- Empty `ExistingEipAllocationId`: create `AWS::EC2::EIP`, associate it, and release it automatically on stack deletion.
- Provided `ExistingEipAllocationId`: skip `AWS::EC2::EIP` creation, associate the provided EIP, and retain it after stack deletion.

## Outputs

- `ElasticIp`
- `ElasticIpAllocationId`
- `ElasticIpMode`
- `BoyarStatusUrl`
- `ManagementStatusUrl`
- `SSHCommand`

## Public direct Launch Stack URL

```text
https://console.aws.amazon.com/cloudformation/home?region=us-east-2#/stacks/create/review?stackName=orbs-boyar-validator&templateURL=https%3A%2F%2Fkryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com%2Forbs-boyar-aws-launcher%2Ftemplate-medium-ami-direct.yaml
```

## Public direct TemplateURL

```text
https://kryp-labs-orbs-boyar-cloudformation-617775257107-us-east-2.s3.us-east-2.amazonaws.com/orbs-boyar-aws-launcher/template-medium-ami-direct.yaml
```

## Validation

- Local CloudFormation `validate-template` passed for `cloudformation/template-medium-ami-direct.yaml`.
- Public S3 TemplateURL validation passed.
- Launch Stack URL uses the public direct TemplateURL.
- Parameters render correctly, including `ExistingEipAllocationId` and `PrivateKeyNoLeading0x`.
- Safety scan passed: no `set -x`, no pre-signed URLs, and no exact local secret values from the build environment.

## Test results

Two direct-input validation stacks were deployed and deleted.

### A. Automatic EIP mode

- Stack: `orbs-boyar-eip-auto-20260518222041`
- `ExistingEipAllocationId`: empty/default
- Output `ElasticIpMode`: `AutoCreatedEip`
- EIP allocated and associated: yes
- `BoyarStatusUrl` via EIP: HTTP 200
- `ManagementStatusUrl` via EIP: HTTP 200

### B. Reused EIP mode

- Stack: `orbs-boyar-eip-reuse-20260518222407`
- Preallocated test EIP was passed as `ExistingEipAllocationId`
- Output `ElasticIpMode`: `ReusedExistingEip`
- Reused EIP public IP matched the preallocated EIP before and during stack use
- `BoyarStatusUrl` via reused EIP: HTTP 200
- `ManagementStatusUrl` via reused EIP: HTTP 200

No private key or RPC endpoint values are included in this report.

## Cleanup behavior

- Auto-created EIP mode: stack deletion released the auto-created EIP allocation.
- Reused EIP mode: stack deletion retained the reused EIP and left it unassociated as expected.
- The temporary preallocated reused-mode EIP was released after validation.
- No intentional orphan EIP remained from the validation run.

## Beginner-friendliness

The Launch Stack remains beginner-friendly because `ExistingEipAllocationId` defaults to empty. Users who do not understand EIP reuse can leave it blank and get the same automatic EIP lifecycle as before.
