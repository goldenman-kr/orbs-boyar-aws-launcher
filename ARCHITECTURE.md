# Architecture

KRYP Labs Orbs Boyar AWS Launcher uses a Medium AMI model.

## Medium AMI model

The AMI contains stable, non-secret components:

- Ubuntu Server 22.04 LTS x86_64
- Base OS dependencies
- Docker Engine and Docker Compose plugin
- Orbs Boyar binary
- Installer files under `/opt/orbs/ami_creator`

The AMI does not contain validator-specific secrets or generated runtime configuration.

## First boot

CloudFormation launches EC2 from the AMI and UserData performs only runtime configuration:

1. Fetch the validator private key from AWS Secrets Manager using the EC2 instance role.
2. Create `/opt/orbs/ami_creator/.env` with the Ethereum endpoint, node address, and private key.
3. Set `.env` permissions to `root:root 0600`.
4. Run `install.sh --configure-and-start` with `ORBS_ENV_FILE=/opt/orbs/ami_creator/.env`.

## Why not a fat AMI

The launcher intentionally does not bake Docker Swarm state, generated keys, management config, logs, or active Boyar service state into the AMI. Those values are host-specific and safer to generate on first boot.
