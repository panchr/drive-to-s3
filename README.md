# drive-to-s3

This project is a CloudFormation stack that provides automatic backups of Google Drive contents
to Amazon S3, using [rclone](https://rclone.org/).

It currently supports creating the following:

* An S3 bucket to store backups in,
* An ECS task running `rclone` to perform the backup,
* An Event rule that triggers the above ECS task on a cron schedule,
* A logging group that the ECS task logs to,
* and all of the IAM roles and networking to support this.

Backups are stored under the `/backup` prefix in the bucket.

## Quick Start
Deploy the stack using the AWS CLI:

```
aws cloudformation create-stack --stack-name drive-backup --capabilities CAPABILITY_IAM --template-body file://stack.yml
```

Note that `--capabilities CAPABILITY_IAM` is required becaues the template creates IAM roles and policies.

Once the stack is created, you can add the configuration below.

## Configuration

You will need a configuration file stored in the S3 bucket created by the stack, under the path

```
/config/rclone.env
```

*Without the configuration, backups will not be performed.*

The file should be of the format:

```
RCLONE_CONFIG_DRIVE_TYPE=drive
RCLONE_CONFIG_DRIVE_SCOPE=drive.readonly
RCLONE_CONFIG_DRIVE_TOKEN=<ACCESS_TOKEN>

RCLONE_CONFIG_S3_TYPE=s3
RCLONE_CONFIG_S3_PROVIDER=AWS
RCLONE_CONFIG_S3_REGION=<AWS_REGION>
RCLONE_CONFIG_S3_SERVER_SIDE_ENCRYPTION=AES256
RCLONE_CONFIG_S3_STORAGE_CLASS=STANDARD
RCLONE_S3_NO_CHECK_BUCKET=true
RCLONE_CONFIG_S3_ENV_AUTH=true

RCLONE_FAST_LIST=true
RCLONE_IGNORE_ERRORS=true
RCLONE_TRACK_RENAMES=true
RCLONE_TRANSFERS=16
```

