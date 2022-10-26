## About

A Github Action Workflow that builds a static website from a Javascript project using NPM and then synchronizes the distribution directory to an S3 bucket.

This workflow requires the arn of one (or two) CICD role(s) that can be assumed in order to push and pull images from the ECR registries. Follow the [Github docs](https://docs.github.com/en/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-amazon-web-services) to setup an OpenID provider on AWS and configure an IAM role that can be assumed by that provider. The role(s) must have the following policy attached.

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowECRPushPull",
            "Effect": "Allow",
            "Action": [
                "ecr:UploadLayerPart",
                "ecr:PutImage",
                "ecr:InitiateLayerUpload",
                "ecr:GetDownloadUrlForLayer",
                "ecr:GetAuthorizationToken",
                "ecr:CompleteLayerUpload",
                "ecr:BatchGetImage",
                "ecr:BatchCheckLayerAvailability"
            ],
            "Resource": "*"
        }
    ]
}
```

## Usage

**Basic:**
```
jobs:
  publish:
    uses: nax-tech/ntg-workflows/aws/push_to_ecr.yaml@v1
    with:
      publish_ci_role: arn:aws:iam::123456789012:role/MyAmazingDeployRole
```

**With Different Base Repository:**
```
jobs:
  publish:
    uses: nax-tech/ntg-workflows/aws/push_to_ecr.yaml@v1
    with:
      base_ci_role: arn:aws:iam::543210987654:role/MyAmazingDeployRole
      publish_ci_role: arn:aws:iam::123456789012:role/MyAmazingDeployRole
```

**With Build Args:**
```
jobs:
  publish:
    uses: nax-tech/ntg-workflows/aws/push_to_ecr.yaml@v1
    with:
      publish_ci_role: arn:aws:iam::123456789012:role/MyAmazingDeployRole
      build_args: |
        SECRET_TOKEN=${{ secrets.SECRET_TOKEN }}
```

## Inputs

| name | required | description |
| --- | --- | --- |
| `image_name` | false | The name of the built image. **Default: The name of the repository in _org/repo_** |
| `image_tag` | false | The tag to assign to the built image. **Default: The name of the branch that triggered the workflow.** |
| `aws_region` | false | The region of the ECR repositories. **Default: us-west-2** |
| `base_ci_role` | false | The ARN of the CICD role used to pull the base image. Only needed if the base image is on a private ECR repository. |
| `publish_ci_role` | true | The ARN of the CICD role used to push the image to ECR. |
| `directory` | false | The directory of the codebase. Used as the context of the `docker build`. **Default: The root of the repository** |
| `dockerfile` | false | The name of the dockerfile to use. **Default: Dockerfile** |
| `build_args` | false | A list of the optional build args to pass to `docker build` |

## Secrets

None

## Outputs

| name | description |
| --- | --- |
| `publish` | The result of the publish job. This can be included in _slack/notify.yaml_ to insert the standard emoji. |
