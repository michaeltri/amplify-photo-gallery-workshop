+++
title = "Installs & Configs"
chapter = false
weight = 20
+++

Before we begin coding, there are a few things we need to install, update, and configure in the Cloud9 environment.

### Installing and updating

In the Cloud9 terminal, **run the following commands** to install and update some software we'll be using for this workshop:

```bash
# Update the AWS CLI to the latest version
pip install --user --upgrade awscli

# Install latest LTS version of Node.js
nvm install --lts 12
nvm alias default 12

# Install the AWS Amplify CLI
npm install -g @aws-amplify/cli

# Install jq
sudo yum install jq -y

```

{{% notice note %}}
These commands will take a few minutes to finish.
{{% /notice %}}

### Configuring a default region 

A best practice is to deploy your infrastructure close to your customers, let's configure a default AWS region for this workshop : **Frankfurt** (*eu-central-1*) for Europe or Northern Virginia (*us-east-1*) for North America.

**Create an AWS config file**, run:

{{% tabs %}}

{{% tab "eu-central-1" "Frankfurt" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=eu-central-1
END
```
{{% /tab %}}

{{% tab "us-east-1" "Virginia" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=us-east-1
END
```
{{% /tab %}}

{{% tab "us-west-2" "Oregon" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=us-west-2
END
```
{{% /tab %}}

{{% tab "eu-west-1" "Ireland" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=eu-west-1
END
```
{{% /tab %}}

{{% tab  "ap-southeast-1"  "Singapore" %}}
```bash
cat <<END > ~/.aws/config
[default]
region=ap-southeast-1
END
```
{{% /tab %}}
{{% /tabs %}}

{{% notice note %}}
The AWS Amplify CLI is a toolchain which includes a robust feature set for simplifying mobile and web application development. The step above took care of installing it, but we also need to configure it. It needs to know what region to work with, and it determines this by looking for the *~/.aws/config* file. Cloud9 takes care of making sure we have valid Administrator credentials in the *~/.aws/credentials* file, but it doesn't create *~/.aws/config* for us.
{{% /notice %}}