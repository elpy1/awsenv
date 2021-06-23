# awsenv
Run a command or return credentials for configured AWS CLI profiles (AWS SSO, assume-role)

## Info
Tool I use daily in conjunction with other tools such as `packer` or `terraform`. Can be used either to return AWS short-term credentials or run commands against a configured AWS profile (with required environment variables set). If you execute `bash`, the script sets `PS1` in your environment (with `awsenv/profile` in green) to indicate the AWS profile you're working with. If your AWS SSO credentials are expired the script runs `aws sso login` automatically.

## Requirements
- `python3`
- `aws` (https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2-linux.html#cliv2-linux-install)

## Installation
- clone this `git` repo or download `awsenv`
- move `awsenv` to `~/.local/bin` or similar
- add to path if need `export PATH="$HOME/.local/bin${PATH:+:${PATH}}"`

## Examples

### Config
example commands below are executed using the following configuration (see aws-mfa):
```
[elpy1@testbox ~]$ cat ~/.aws/credentials 
[testauth-long-term]
aws_access_key_id = AKIxxxxxxxxxxxJO65WL
aws_secret_access_key = D7YHiK1wpxxxxxxxxxxxxxxxxxxxxxxxVuSe2WHu
aws_mfa_device = arn:aws:iam::12xxxxxxxxx0:mfa/test-user
```
```
[elpy1@testbox ~]$ cat ~/.aws/config
[default]
region = ap-southeast-2

[profile test-profile]
region = ap-southeast-2
role_arn = arn:aws:iam::123456789123:role/test-role-admin
source_profile = testauth
```

#### Usage
return credentials:
```
[elpy1@testbox ~]$ awsenv test-profile
export AWS_ACCESS_KEY_ID="ASIAxxxxxxxxxxxx7NGH"
export AWS_SECRET_ACCESS_KEY="SVUtKxxxxxxxxxxxxxxxxxxxxxxxxZJZDlWoEBZC"
export AWS_SESSION_TOKEN="FwoGZXIvYXdzEPr//////////wExxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx3Tw1idmBDSTlJL/RAZ6RZHzhG7e6gw4tJLkoDC2PVaRB8s+Tmkv53OfzJmAa/xWWeMeieGAKDxeagtYf4H/BNL01I5sqTOQYRTMrHfxMAb5dDheBJfNz/5XrhrMWmXBpehyma/DqwwZbTxCMhRAAJbljhx9EZvMyED8Y04+ijrAAog1QAc0kI9wCOAtEWjo2Ci7xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxGEnmMofroKqO/hBaMo"

```

run command with AWS credentials set:
```
[elpy1@testbox ~]$ awsenv test-profile -- aws sts get-caller-identity
{
    "UserId": "AROAxxxxxxxxxxxxIWXS2:botocore-session-15xxxxxx34",
    "Account": "1xxxxxxxxxx3",
    "Arn": "arn:aws:sts::1xxxxxxxxxx3:assumed-role/test-role-admin/botocore-session-15xxxxxx34"
}

[elpy1@testbox ~]$ awsenv test-profile -- ipython
...

[elpy1@testbox ~]$ awsenv test-profile -- terraform plan
...
```
execute `bash` with AWS credentials set in local environment:
```
[elpy1@testbox ~]$ awsenv test-profile -- bash
[elpy@awsenv/test-profile ~]$ aws sts get-caller-identity
{
    "UserId": "AROAxxxxxxxxxxxxIWXS2:botocore-session-15xxxxxx34",
    "Account": "1xxxxxxxxxx3",
    "Arn": "arn:aws:sts::1xxxxxxxxxx3:assumed-role/test-role-admin/botocore-session-15xxxxxx34"
}
[elpy@awsenv/test-profile ~]$ exit
[elpy1@testbox ~]$
```
or `eval`:
```
[elpy1@testbox ~]$ eval $(awsenv test-profile)
[elpy1@testbox ~]$ aws sts get-caller-identity
{
    "UserId": "AROAxxxxxxxxxxxxIWXS2:botocore-session-15xxxxxx34",
    "Account": "1xxxxxxxxxx3",
    "Arn": "arn:aws:sts::1xxxxxxxxxx3:assumed-role/test-role-admin/botocore-session-15xxxxxx34"
}
```
