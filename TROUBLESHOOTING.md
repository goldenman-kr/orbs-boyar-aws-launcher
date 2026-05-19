# Troubleshooting

## Autonet resources

The default template creates its own VPC, public subnet, Internet Gateway, route table, Security Group, EC2 instance, and Elastic IP. If deletion fails, check these stack-owned resources first.

## Stack does not reach CREATE_COMPLETE

Check stack events:

```bash
aws cloudformation describe-stack-events \
  --region us-east-2 \
  --stack-name <stack-name>
```

Common causes:

- AWS account limits for VPC, subnet, route table, Internet Gateway, Elastic IP, or EC2 resources
- Invalid optional EC2 Key Pair name when `KeyName` is provided
- Invalid AMI region
- Missing IAM permissions for EC2/VPC/EIP resource creation in the launching AWS principal
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

By default, the Marketplace-oriented autonet stack uses secure ingress `AccessCidr=127.0.0.1/32`. To reach status endpoints externally, enter your trusted public IP as `x.x.x.x/32`, another CIDR you control, or explicitly `0.0.0.0/0` if you intentionally want public access.

## Elastic IP issues

Check the `ElasticIp`, `ElasticIpAllocationId`, and `ElasticIpMode` stack outputs and use `ElasticIp` for status URLs and SSH. If stack creation rolls back in auto-created mode, CloudFormation should release the EIP automatically. If `ExistingEipAllocationId` was provided, the EIP is user-managed and intentionally remains allocated after stack deletion.

## Status URLs are not reachable

Check:

- The stack has an `ElasticIp` output and the EIP is associated with the instance.
- If using reuse mode, `ExistingEipAllocationId` exists in the same region and is not already associated with another instance.
- `AccessCidr` allows your client IP. The secure default `127.0.0.1/32` will not allow external clients.
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
aws ec2 describe-addresses --region us-east-2 --allocation-ids <allocation-id>
```

If using the Secrets Manager template, the Secrets Manager secret is user-managed and is not deleted by the template.
