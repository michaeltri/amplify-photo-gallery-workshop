+++
title = "Create a Cloud9 Workspace"
chapter = false
weight = 10
+++

AWS Cloud9 is a cloud-based integrated development environment (IDE) that lets you write, run, and debug your code with just a browser. It includes a code editor, debugger, and terminal. Cloud9 comes prepackaged with essential tools for popular programming languages, including JavaScript, Python, PHP, and more, so you don't need to install files or configure your development machine to start new projects.

{{% notice warning %}}
The Cloud9 workspace should be built by an IAM user with Administrator privileges,
not the root account user. Please ensure you are logged in as an IAM user, not the root
account user.
{{% /notice %}}

{{% notice info %}}
Ad blockers, JavaScript disablers, and tracking blockers should be disabled for
the cloud9 domain, otherwise connecting to the workspace might be impacted.
{{% /notice %}}

### Create a new environment

1. Go to the [Cloud9 web console](https://eu-central-1.console.aws.amazon.com/cloud9/home?region=eu-central-1#).
1. At the top right corner of the console, make sure you're using one of these regions: **Frankfurt (eu-central-1)**, Virginia (us-east-1), Oregon (us-west-2), Ireland (eu-west-1) or Singapore (ap-southeast-1)
1. Select **Create environment**
1. Name it **workshop**, and go to the **Next step**
1. Select **Create a new instance for environment (EC2)** and pick **t3.medium**
2. Leave all of the environment settings as they are, and go to the **Next step**
3. Click **Create environment**

### Clean up the layout

When the environment comes up, customize the layout by closing the **welcome tab**
and **lower work area**, and opening a new **terminal** tab in the main work area:
![c9before](/images/c9before.png)

Your workspace should now look like this:
![c9after](/images/c9after.png)

If you like this theme, you can choose it yourself by selecting **View / Themes / Solarized / Solarized Dark**
in the Cloud9 workspace menu.
