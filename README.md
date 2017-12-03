# GitLab CI multi runner on AWS spot inctances

This repo contains a terraform sample script to create GitLab CI multi runners on AWS spot instances.

## Setup

### Terraform
On mac simple install tfenv using brew.
```
brew install tfenv
```
Next intall a terraform version.
```
tfenv install <version>
```

### AWS
To run the terraform scripts you need to have AWS keys.
Example file:
```
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
```


## Configuration
Update the variables in `terraform.tfvars` to your needs and add the folling variables, see below guidelines how to obtain the token.
```
runner_name = "NAME_OF_YOUR_RUNNER"
gitlab_url = "GIT_LAB_URL"
runner_token = "RUNNER_TOKEN"
```

### Obtain GitLab runner token
Currently register a new runner is a manual process. See the GitLab Runner [documentation](https://docs.gitlab.com/runner/register/index.html#docker) for more details.
```
docker run -it --rm gitlab/gitlab-runner register
```
Provice the details in teh interactive terminal. Once done the token cen found in the GitLab runners section, choose edit to get the token.


### AWS keys
Run `./init.sh` to create SSH keys for the runner instance.

## Create runner
Terraform commands to manage the ECS cluster

Run `terraform init` to inialize terraform. Next you can run `terraform plan` to inspect the resources that will be create.

To create the runner run:
```
terrafrom apply
```
To destroy runner:
```
terraform desroy
```

## Inputs

| Name | Description | Type | Default | Required |
|------|-------------|:----:|:-----:|:-----:|
| amazon_optimized_amis | AMI map per region-zone for the gitlab-runner instance AMI. | map | `<map>` | no |
| aws_region | AWS region. | string | - | yes |
| cache_user | User name of the user to create to write and read to the s3 cache. | string | `cache_user` | no |
| docker_machine_instance_type | Instance type used for the instances hosting docker-machine. | string | `m3.large` | no |
| docker_machine_spot_price_bid | Spot price bid. | string | `0.03` | no |
| docker_machine_user | User name for the user to create spot instances to host docker-machine. | string | `docker-machine` | no |
| environment | A name that indentifies the environment, will used as prefix and for taggin. | string | - | yes |
| instance_type | Instance type used for the gitlab-runner. | string | `t2.micro` | no |
| runner_concurrent | Concurrent value for the runners, will be used in the runner config.toml | string | `10` | no |
| runner_gitlab_url | URL of the gitlab instance to connect to. | string | - | yes |
| runner_limit | Limit for the runners, will be used in the runner config.toml | string | `1` | no |
| runner_name | Name of the runner, will be used in the runner config.toml | string | - | yes |
| runner_token | Token for the runner, will be used in the runner config.toml | string | - | yes |
| ssh_key_file_pub | File contians the public key used for the gitlab-runner. | string | - | yes |
| subnet_id_gitlab_runner | Subnet used for hosting the gitlab-runner. | string | - | yes |
| subnet_id_runners | Subnet used to hosts the docker-machine runners. | string | - | yes |
| vpc_id | The VPC that is used for the instances. | string | - | yes |


## Notes:
- Shared cache not configured yet.
- A user is create for the gitlab-runner / docker-machine to create ec2 instances, the permission NOT restrictive enough.