## About

A Github Action Workflow that builds a static website from a Javascript project using NPM and then synchronizes the distribution directory to an S3 bucket.

This workflow requires the arn of a CICD role that can be assumed in order to synchronize a local directory to the specified S3 bucket. Follow the [Github docs](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) to setup an OpenID provider on AWS and configure an IAM role that can be assumed by that provider. The role must have the following policy attached, where `${bucket_name}` should be the same as the value for the `bucket_name` input.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowS3Access",
            "Effect": "Allow",
            "Action": [
                "s3:PutObject",
                "s3:ListBucket",
                "s3:GetObjectVersion",
                "s3:GetObject",
                "s3:DeleteObjectVersion",
                "s3:DeleteObject"
            ],
            "Resource": [
                "arn:aws:s3:::${bucket_name}/*",
                "arn:aws:s3:::${bucket_name}"
            ]
        }
    ]
}
```

## Usage

```
jobs:
  validate:
    uses: nax-tech/ntg-workflows/npm/aws_static_website.yaml@v1
    with:
      bucket_name: hello-my-amazing-world
      cicd_role_arn: arn:aws:iam::123456789012:role/MyAmazingDeployRole
```

## Inputs

| name | required | description |
| --- | --- | --- |
| `bucket_name` | true | The name of the S3 bucket that will host the static website — or the backing bucket if behind Cloudfront. |
| `bucket_region` | false | The region the bucket resides in. **Default: us-west-2** |
| `cicd_role_arn` | true | The ARN of the CICD role to assume. |
| `node_version` | false | The version of NPM to install. **Default: 16.14.2** |
| `install_command` | false | The command to install the required NPM packages. **Default: npm install** |
| `build_command` | false | The command to build the project. Use `dist_path` to control where NPM places the built files. **Default: npm run build** |
| `directory` | false | The directory of the codebase. **Default: The root of the repository** |
| `dist_path` | false | The local directory that should be synchronized to the S3 bucket. NPM will place the built files in this directory. **Default: The `dist` directory** |

## Secrets

None

## Outputs

| name | description |
| --- | --- |
| `build` | The result of the build job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
| `sync` | The result of the sync job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
