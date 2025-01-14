+++
title = "Setting Up the Back End"
chapter = false
weight = 10
+++

Now that we have a simple React app, let's let users sign up and sign in to our app. They won't be able to do anything yet, but it will be helpful to have this in place so that when we add in the ability to query our backend API, we'll know which users are accessing our system.

Here's what the sign-in screen will look like:
![Here's what the sign-in screen looks like](/images/app-signin-screen.png?classes=border)

{{% notice note %}}
This will create a new local configuration for us which we can use to set up an [Amazon Cognito](https://aws.amazon.com/cognito/) User Pool to act as the backend for letting users sign up and sign in. (More about Amazon Cognito and what a User Pool is below.) If you want to read more about this step, take a look at the 'Installation and Configuration' steps from the [AWS Amplify Authentication guide](https://aws-amplify.github.io/amplify-js/media/authentication_guide.html).
{{% /notice %}}

### Adding authentication

1. **Run** `amplify add auth` to add authentication to the app

1. **Select Default Configuration** when asked if you'd like to use the default authentication and security configuration
   
1. **Select Username** when asked how you want users to sign in
   
1. **Select "No, I am done."** when asked about advanced settings.

2. **Run** `amplify push` to create these changes in the cloud

2. Confirm you want Amplify to make changes in the cloud for you.

3. Wait for the provisioning to complete. This will take a few minutes.

{{% notice note %}}
The Amplify CLI will take care of provisioning the appropriate cloud resources and it will update src/aws-exports.js with all of the configuration data we need to be able to use the cloud resources in our app.
{{% /notice %}}

Congratulations! You've just created a serverless backend for user registration and authorization capable of scaling to millions of users with Amazon Cognito. 

### View your changes in the Amplify console

![Amp Auth](/images/amplify_backend_auth.png)

![Amp Auth](/images/amplify_backend_auth_details.png)

{{% notice tip %}}
Amazon Cognito lets you add user sign-up, sign-in, and access control to your web and mobile apps quickly and easily. We just made a User Pool, which is a secure user directory that will let our users sign in with the username and password pair they create during registration. Amazon Cognito (and the Amplify CLI) also supports configuring sign-in with social identity providers, such as Facebook, Google, and Amazon, and enterprise identity providers via SAML 2.0. If you'd like to learn more, we have a lot more information on the [Amazon Cognito Developer Resources page](https://aws.amazon.com/cognito/dev-resources/) as well as the [AWS Amplify Authentication documentation.](https://aws-amplify.github.io/amplify-js/media/authentication_guide#federated-identities-social-sign-in)
{{% /notice %}}