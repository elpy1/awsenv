# awsenv
Run commands or return credentials for different AWS profiles/roles

## Info
Tool I use in conjuction with `aws-mfa` to improve workflow. Can be used to return AWS assumerole credentials or run commands with required environment variables set. If you run `bash` you'll have `PS1` set in your environment (with `awsenv/profile` in green) to indicate the account you're working in. Will execute `aws-mfa` to re-prompt for mfa if command fails.

### Requirements
- awscli
- aws-mfa -> https://github.com/broamski/aws-mfa

### Installation
- git clone
- move `awsenv` to `~/.local/bin` or similar
- add directory to path if need `export PATH="$HOME/.local/bin${PATH:+:${PATH}}"`
- configure `~/.aws/{config,credentials}`. If using MFA, follow `aws-mfa` documentation

## Examples

### Config
example commands below are executed with the following configuration (see aws-mfa):
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
