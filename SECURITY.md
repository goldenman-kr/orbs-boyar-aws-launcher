# Security Notes

## Secrets are not baked into the AMI

The Medium AMI contains only stable, non-secret components. It does not contain:

- `.env`
- validator private keys
- generated `/opt/orbs/keys.json`
- generated `/opt/orbs/mgmt.json`
- active `boyar.service` runtime state
- validator logs

## Private key handling

The release-candidate CloudFormation template does not accept the validator private key as a raw parameter. It accepts `PrivateKeySecretArn` and fetches the key from AWS Secrets Manager at first boot.

The first-boot process writes `/opt/orbs/ami_creator/.env` with `root:root 0600` permissions and then runs `install.sh --configure-and-start`.

## IAM least-privilege model

The EC2 instance role has an inline policy with only:

```yaml
Action:
  - secretsmanager:GetSecretValue
Resource: !Ref PrivateKeySecretArn
```

This limits the instance to reading only the specified secret ARN.

## Network exposure model

The launcher default exposes all currently configured ingress ports to `0.0.0.0/0`:

- `tcp/22` for SSH
- `tcp/80` for Boyar HTTP/status access
- `tcp/7666` for management-service/status access

The public node/status endpoints may need broad access for blockchain node accessibility and early-access testing. SSH is also public by default in this launcher, so users are responsible for securing their EC2 key pair, AWS credentials, IAM permissions, and validator secret. Advanced users can restrict `AccessCidr` to a narrower CIDR such as a trusted office range or a single `/32` IP.

## What users should not do

Do not:

- Put the raw private key into CloudFormation parameters.
- Store private keys in Git, shell history, issue trackers, logs, screenshots, or chat messages.
- Share the generated `.env` file.
- Treat the default public `AccessCidr` as a convenience only; do not treat it as a substitute for key management. SSH is exposed by default, so protect your EC2 private key and AWS account.
- Reuse validator private keys across unrelated environments.
- Publish stack events, UserData, or support bundles without reviewing them for sensitive data.

## Known MVP limitations

- Region support is currently `us-east-2` only.
- The EC2 root volume is not encrypted by default in the current template.
- The default `AccessCidr` is `0.0.0.0/0`, so SSH (`22`) and node/status endpoints (`80`, `7666`) are publicly reachable unless you restrict the parameter.
- The template does not create the Secrets Manager secret.
- The template does not configure TLS, domain names, monitoring, alerting, backups, or automatic secret rotation.
- The UserData implementation signs the Secrets Manager API request directly using IMDSv2 role credentials to avoid adding AWS CLI installation to the AMI. This should be reviewed before public listing.
