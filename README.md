# AWS IAM Secure Access Project

## Project Overview
This project demonstrates how to implement secure access management in AWS using IAM users, groups, custom policies, EC2 IAM roles, Amazon S3, and Multi-Factor Authentication (MFA).

The main objective is to follow the **Principle of Least Privilege**, ensuring that users and EC2 instances receive only the permissions required to perform their tasks.

## AWS Services Used
- AWS IAM (Identity and Access Management)
- Amazon EC2
- Amazon S3
- AWS CLI
- Multi-Factor Authentication (MFA)

## Project Architecture

An IAM user receives read-only S3 permissions through an IAM group and a custom policy. An EC2 instance uses an IAM role to access the S3 bucket securely without storing AWS access keys on the instance.

## Implementation Steps

### 1. Create an S3 Bucket
- Created an S3 bucket named `secure-iam-project-2026-unique`.
- Uploaded a sample file named `test.txt`.
- Used this bucket to test access permissions.

### 2. Create an IAM Group
- Created an IAM group named `S3-ReadOnly-Group`.
- Added the IAM user `s3-readonly-user` to the group.

### 3. Create a Custom IAM Policy
Created a customer-managed policy named `S3-ReadOnly-Custom-Policy`.

The policy grants:
- `s3:ListAllMyBuckets` – List S3 bucket names.
- `s3:ListBucket` – List objects in the specified bucket.
- `s3:GetBucketLocation` – View bucket location.
- `s3:GetObject` – Download objects from the specified bucket.

It does not grant:
- `s3:PutObject`
- `s3:DeleteObject`

### 4. Attach Policy to IAM Group
Attached the custom policy to `S3-ReadOnly-Group`, allowing the group member to access the specified S3 bucket with read-only permissions.

### 5. Create an EC2 IAM Role
- Created an IAM role named `EC2-S3-Access-Role`.
- Selected EC2 as the trusted entity.
- Attached the `S3-ReadOnly-Custom-Policy`.
- Attached the role to the EC2 instance named `IAM-S3-Test-Instance`.

### 6. Verify IAM Role Using AWS CLI

Connected to the EC2 instance using EC2 Instance Connect.

Checked AWS CLI version:

```bash
aws --version
```

Verified the identity associated with the EC2 instance:

```bash
aws sts get-caller-identity
```

Listed available S3 buckets:

```bash
aws s3 ls
```

Listed objects in the target bucket:

```bash
aws s3 ls s3://secure-iam-project-2026-unique/
```

Downloaded the sample object:

```bash
aws s3 cp s3://secure-iam-project-2026-unique/test.txt /home/ec2-user/test.txt
```

Read the downloaded file:

```bash
cat /home/ec2-user/test.txt
```

### 7. Test Write Restrictions

Created a test file:

```bash
echo "Testing IAM role write restriction" > upload-test.txt
```

Attempted to upload it to S3:

```bash
aws s3 cp upload-test.txt s3://secure-iam-project-2026-unique/
```

**Expected result:** Access Denied because the IAM role does not have `s3:PutObject` permission.

### 8. Configure MFA
- Assigned an MFA device to the IAM user `s3-readonly-user`.
- Verified that AWS confirmed the MFA device assignment.

## Project Testing Results

| Test Case | Expected Result | Status |
|---|---|---|
| List S3 buckets | Allowed | Passed |
| Access target bucket | Allowed | Passed |
| Download `test.txt` | Allowed | Passed |
| Upload a new object | Denied | Passed |
| Delete an object | Denied | Verified separately |
| Verify EC2 IAM role | Role assumed successfully | Passed |
| Configure IAM user MFA | MFA device assigned | Passed |

## Security Best Practices Implemented
- Followed the Principle of Least Privilege.
- Used IAM groups to manage user permissions.
- Used a customer-managed IAM policy.
- Used an EC2 IAM role instead of storing access keys on the instance.
- Restricted S3 write permissions.
- Enabled MFA for an IAM user.

## Key Learnings
- Understanding IAM users, groups, roles, and policies.
- Implementing S3 bucket-level and object-level permissions.
- Configuring EC2 IAM roles.
- Using AWS CLI to verify IAM identities and S3 access.
- Understanding access denied errors and permission boundaries.

## Conclusion
Successfully implemented an AWS IAM secure access environment using IAM users, groups, custom policies, EC2 roles, S3, and MFA.

The project demonstrates how AWS IAM can be used to control access securely while following least-privilege principles.

**Project Status:** Completed

**AWS Region:** Asia Pacific (Mumbai) – `ap-south-1`
