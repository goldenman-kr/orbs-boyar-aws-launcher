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

The early-access direct-input CloudFormation template accepts `PrivateKeyNoLeading0x` as a `NoEcho` parameter. `NoEcho` hides the value in the CloudFormation UI and API outputs, but the secret still exists temporarily in CloudFormation parameter handling during stack creation.

The first-boot process writes `/opt/orbs/ami_creator/.env` with `root:root 0600` permissions and then runs `install.sh --configure-and-start`.

The separate Secrets Manager template is retained for higher-security production and future Marketplace use. In that mode, CloudFormation accepts `PrivateKeySecretArn` and the EC2 instance fetches the key from AWS Secrets Manager at first boot.

## IAM model

The direct-input template does not require Secrets Manager read permissions or an EC2 instance role for private key retrieval.

The Secrets Manager template keeps the least-privilege model: the EC2 instance role can call only `secretsmanager:GetSecretValue` on the specified `PrivateKeySecretArn`.

## Elastic IP lifecycle

The launcher creates a new Elastic IP and associates it with the validator instance by default. This gives testers a stable public address while the stack exists. CloudFormation releases auto-created EIPs when the stack is deleted; users should record the `ElasticIp` and `ElasticIpAllocationId` outputs for operational checks. Advanced users can pass `ExistingEipAllocationId` to preserve the same node IP across reinstall/recovery. Reused EIPs are not released by the stack and remain the user's responsibility, including any AWS charges.

## Autonet deployment model

The default Marketplace-oriented template creates an isolated VPC, public subnet, Internet Gateway, route table, Security Group, EC2 instance, and Elastic IP automatically. Users do not need to select an existing VPC or subnet. SSH key pair input is optional in the GitHub easy-mode template; leaving `KeyName` empty launches without SSH key-pair access. The Marketplace template requires `KeyName` for reviewer SSH validation while still enforcing key-only SSH and `PasswordAuthentication no`.

## Network exposure model

The Marketplace-oriented direct-input template uses a secure ingress default: `AccessCidr=127.0.0.1/32`. The configured CIDR controls all currently configured ingress ports:

- `tcp/22` for SSH
- `tcp/80` for Boyar HTTP/status access
- `tcp/7666` for management-service/status access

Public access is optional and user-controlled. Use `x.x.x.x/32` for a single trusted IP, a trusted office/VPN CIDR for a range, or `0.0.0.0/0` only if you intentionally want all IPs to reach SSH and node/status endpoints. Users remain responsible for securing their EC2 key pair, AWS credentials, IAM permissions, and validator secret.

## What users should not do

Do not:

- Put private keys into non-NoEcho CloudFormation parameters, stack descriptions, logs, tickets, screenshots, or chat messages.
- Store private keys in Git, shell history, issue trackers, logs, screenshots, or chat messages.
- Share the generated `.env` file.
- Do not enter `0.0.0.0/0` for `AccessCidr` unless you intentionally want public access from all IPs. Even with restricted CIDRs, protect your EC2 private key and AWS account.
- Reuse validator private keys across unrelated environments.
- Publish stack events, UserData, or support bundles without reviewing them for sensitive data.

## Known MVP limitations

- Region support is currently `us-east-2` only.
- The EC2 root volume is not encrypted by default in the current template.
- The direct-input template default `AccessCidr` is `127.0.0.1/32` for secure Marketplace review. Users can explicitly choose broader access, including `0.0.0.0/0`, if desired.
- The direct-input template does not create or require a Secrets Manager secret; the Secrets Manager template still requires users to create one separately.
- The template does not configure TLS, domain names, monitoring, alerting, backups, or automatic secret rotation.
- The UserData implementation signs the Secrets Manager API request directly using IMDSv2 role credentials to avoid adding AWS CLI installation to the AMI. This should be reviewed before public listing.
