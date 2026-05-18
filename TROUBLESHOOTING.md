# Troubleshooting

## Stack does not reach CREATE_COMPLETE

Check stack events:

```bash
aws cloudformation describe-stack-events \
  --region us-east-2 \
  --stack-name <stack-name>
```

Common causes:

- Wrong VPC/subnet region
- Missing EC2 Key Pair
- Invalid AMI region
- IAM capability not acknowledged; use `--capabilities CAPABILITY_IAM`
- Invalid direct private key format, or invalid Secrets Manager ARN if using the Secrets Manager template

## First boot fails before services start

SSH to the instance and inspect:

```bash
sudo tail -n 200 /var/log/orbs-boyar-cloudformation.log
sudo tail -n 200 /var/log/orbs-boyar-install.log
```

The logs should not print private key values. If a log contains sensitive data, stop and treat it as a secret exposure incident.

## Direct private key input fails

Check:

- `PrivateKeyNoLeading0x` is exactly 64 hex characters.
- Do not include a leading `0x` in the CloudFormation parameter.
- The value is entered only in the NoEcho parameter field, not in stack names, descriptions, tags, tickets, logs, or chat.

## Secrets Manager access fails

If using the Secrets Manager template, check:

- `PrivateKeySecretArn` is in `us-east-2`.
- The secret exists and is not scheduled for deletion.
- The secret value is either a raw 64-hex key or JSON with a supported key name.
- The stack-created role policy references the same secret ARN.

## Private key validation fails

The private key must be 64 hex characters. In direct-input mode, enter it without a leading `0x` because the CloudFormation parameter validation expects exactly 64 hex characters.

## Node address validation fails

`NodeAddressWithNoLeading0x` must be exactly 40 hex characters and must not include `0x`.

## Network access defaults

By default, the stack exposes SSH (`22`) and node/status endpoints (`80`, `7666`) publicly with `AccessCidr=0.0.0.0/0`. Users are responsible for securing their EC2 key pair and AWS account. Advanced users can restrict `AccessCidr` if they intentionally want narrower access.

## Elastic IP issues

Check the `ElasticIp` stack output and use it for status URLs and SSH. If stack creation rolls back, CloudFormation should release the EIP automatically. After stack deletion, verify there is no remaining Elastic IP allocated by the stack.

## Status URLs are not reachable

Check:

- The stack has an `ElasticIp` output and the EIP is associated with the instance.
- `AccessCidr` allows your client IP, or is left at the default `0.0.0.0/0`.
- Security Group allows ports `22`, `80`, and `7666` from `AccessCidr`.
- The service has finished its startup transition.

Commands on the instance:

```bash
sudo systemctl status boyar.service --no-pager --full
sudo docker service ls
sudo docker ps --format 'table {{.Names}}\t{{.Image}}\t{{.Status}}'
curl -fsS http://localhost/services/boyar/status
curl -fsS http://localhost:7666/status
```

## Cleanup verification

After deleting the stack, confirm:

```bash
aws ec2 describe-instances --region us-east-2 --instance-ids <instance-id>
aws ec2 describe-volumes --region us-east-2 --filters Name=attachment.instance-id,Values=<instance-id>
aws ec2 describe-addresses --region us-east-2 --filters Name=tag:Project,Values=orbs-boyar-aws-launcher
```

If using the Secrets Manager template, the Secrets Manager secret is user-managed and is not deleted by the template.
