AWS IAM Role - Cross-Service Access (EC2 to S3)

This project builds on a separate IAM role project where a human user assumed a role manually. Here the goal was to test the same trust/permission model but with an AWS service as the principal instead of a person, which is how most real-world role usage actually works.

What was built

An IAM role called EC2-S3ReadOnly-Role with a trust policy that allows the EC2 service (ec2.amazonaws.com) to assume it, and an inline permission policy that only allows s3:GetObject and s3:ListBucket, scoped to a single S3 bucket.

An EC2 instance (Amazon Linux 2023, t3.micro) launched with that role attached as its instance profile.

Why this matters

Normally you would never put AWS access keys inside an EC2 instance. Instance profiles solve that problem by letting AWS hand the instance temporary, automatically rotated credentials tied to a role, with nothing stored on disk and nothing to leak if the instance is compromised. This is the mechanism behind most legitimate service-to-service access in AWS.

Verification

Connected to the instance through EC2 Instance Connect (browser-based, no SSH key involved) and ran the AWS CLI directly from the instance:

aws s3 ls s3://<bucket-name>

The command returned the bucket contents successfully with no credentials configured anywhere on the instance, confirming the role was working purely through the attached instance profile.

Files in this repo

trust-policy.json - the role's trust policy, scoped to the EC2 service
permission-policy.json - the inline permission policy, scoped to one bucket and two read actions

Account ID and bucket name have been replaced with placeholders in the JSON files.

Related concepts

This project pairs with a separate one covering the same trust-policy-vs-permission-policy distinction where a human IAM user assumes a role instead of a service. Together they cover the two most common ways roles get used in practice.
