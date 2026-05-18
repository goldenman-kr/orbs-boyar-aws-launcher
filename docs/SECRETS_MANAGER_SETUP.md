# Secrets Manager Setup

The KRYP Labs Orbs Boyar AWS Launcher uses AWS Secrets Manager for the validator private key. The CloudFormation template accepts a secret ARN instead of a raw private key parameter.

## Secret region

Create the secret in `us-east-2`, the same region as the current AMI and Launch Stack flow.

## Expected secret format

The secret value may be a raw 64-hex private key string:

```text
<64-hex-private-key-without-0x>
```

Or JSON with one of these keys:

```json
{
  "PRIVATE_KEY_NO_LEADING_0x": "<64-hex-private-key-without-0x>"
}
```

Also accepted:

- `privateKeyNoLeading0x`
- `privateKey`

A leading `0x` is tolerated by the first-boot parser and removed before writing the runtime `.env`, but storing the key without `0x` is recommended.

## Create the secret with AWS Console

1. Open AWS Secrets Manager in `us-east-2`.
2. Choose **Store a new secret**.
3. Choose **Other type of secret**.
4. Enter the private key as a plaintext value or as JSON using one of the supported keys.
5. Give the secret a clear name.
6. Save the secret and copy its ARN.
7. Paste only the ARN into the CloudFormation `PrivateKeySecretArn` parameter.

## Create the secret with AWS CLI

Use a secured workflow that avoids leaving the key in shell history. One option is to place the value in a temporary file, create the secret, then securely delete the file according to your operating procedures.

Example command shape:

```bash
aws secretsmanager create-secret \
  --region us-east-2 \
  --name orbs-boyar-validator-private-key \
  --secret-string file://private-key-secret.txt
```

Do not commit `private-key-secret.txt` and do not leave it on disk after use.

## Required IAM permissions

The user or role creating the secret needs appropriate Secrets Manager permissions, such as:

- `secretsmanager:CreateSecret`
- `secretsmanager:DescribeSecret`
- `secretsmanager:GetSecretValue` for verification if needed

The CloudFormation launching principal needs to pass the secret ARN to the stack and create IAM resources with `CAPABILITY_IAM`.

The EC2 role created by the stack receives only:

```yaml
Action:
  - secretsmanager:GetSecretValue
Resource: <PrivateKeySecretArn>
```

## Security warnings

- Never put the raw private key into CloudFormation parameters.
- Never paste the private key into support tickets, screenshots, logs, or chat.
- Restrict access to the secret ARN.
- Rotate or delete unused validator keys according to your operational policy.
- Review CloudTrail and Secrets Manager access logs if you suspect exposure.
- Keep `SSHLocation` restricted to your trusted IP range.
