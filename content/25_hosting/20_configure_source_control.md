+++
title = "Configure Source Control"
chapter = false
weight = 20
+++

### Adding the source to AWS CodeCommit

At this point, we have the base app infrastructure setup. Amplify also provides a Git-based workflow for deploying and hosting fullstack serverless web applications via [Amplify Console](https://aws.amazon.com/amplify/console/).

In this module, we will create a Git repository to manage application source code and deploy the application using Amplify Console. Amplify Console supports several different Git-based repositories as well as manual deploys. For this workshop, we will use [AWS CodeCommit](https://aws.amazon.com/codecommit/).

To get started, we will create a new CodeCommit repostiory and [Git credentials](https://aws.amazon.com/blogs/devops/introducing-git-credentials-a-simple-way-to-connect-to-aws-codecommit-repositories-using-a-static-user-name-and-password/) to access the service. Enter the following commands in the second terminal (you should be in the `photoalbums` directory) tab to create the repository and commit the project to source control:

``` bash
git config --global credential.helper '!aws codecommit credential-helper $@'
```

```bash
git config --global credential.UseHttpPath true
```

```bash
export REPO=$(aws codecommit create-repository --repository-name photoalbums \
        --repository-description "photoalbums workshop" \
        --query 'repositoryMetadata.cloneUrlHttp' \
        --output text)
```

```bash
git remote add origin $REPO
```

```bash
git add .
```

```bash
git commit -m "Initial commit"
```

```bash
git push origin master
```

### AWS CodeCommit

![amplify init](/images/amplify_code_commit.png)