+++
title = "Connect to Amplify"
chapter = false
weight = 20
+++

Next, navigate to [Amplify Console](https://console.aws.amazon.com/amplify/home) and click the "Get Started" button under Deploy.

![Get Started](/images/1_amplify_console_get_started.png)

Select CodeCommit and click "Continue".

![Pick Repository](/images/2_select_codecommit_repo.png)

Select your newly created repository (AmplifyPhotos) and branch (master). Click "Next".

![Add Branch](/images/3_select_repository.png)

If you have not used Amplify Console before, we need to create a new service role. Click the "Create new role" button. A new tab titled "Create role" will open. Follow the Role creation flow as shown in the following screens, clicking on the blue button in the lower corner on each step (accept default selections):

![Create Amplify Service Role](/images/4_create_role_iam_flow.png)

Back in Amplify Console, the screen should look like the following (you may need to refresh the role listing).

![Configure Build](/images/5_configure_build.png)

Click "Next" at the bottom of the page.

Review the settings and click "Save and Deploy."

![Configure Build](/images/7_build_in_progress.png)

After several minutes, Amplify will have deployed the build and provided a link to view the application. Amplify also generates screenshots of the application on various sized mobile devices at the bottom of the page. Take a moment to review the application and screenshots.

> Our deployment uses the same cloud resources and data as we have used for development. In a real-world scenario, we could use Amplify's support for [multiple environments](https://aws.amazon.com/blogs/mobile/amplify-adds-support-for-multiple-environments-custom-resolvers-larger-data-models-and-iam-roles-including-mfa/) to create separate sets of cloud resources for production and other environments.

![Deployed Application](/images/8_finished_deploy.png)

### View the deployed app

![Deployed Application](/images/amplify_deployed_app.png)
### Examine Build Details

Click on 'master' to examine the build output:
![Build Provision](/images/amplify_build_provision.png)


