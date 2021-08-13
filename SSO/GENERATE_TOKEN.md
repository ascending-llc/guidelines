# ASCENDING AWS Account Guide

Configuring the AWS CLI to use AWS Single Sign-On

This feature is available only if you use AWS CLI version 2. It isn't available if you run AWS CLI version 1. For information on how to install version 2, see [Installing, updating, and uninstalling the AWS CLI version 2](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html). 

You can add an AWS SSO enabled profile to your AWS CLI by running the following command, providing your AWS SSO start URL and the AWS Region that hosts the AWS SSO directory.

```
$ aws configure sso
SSO start URL [None]: [None]: https://ascending.awsapps.com/start
SSO region [None]:us-east-1
```

The AWS CLI attempts to open your default browser and begin the login process for your AWS SSO account.

To read more about using CLI with SSO, please read [this](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sso.html) document.