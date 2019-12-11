+++
title = "Adding Cloud Storage"
chapter = false
weight = 30
+++

We'll need a place to store all of the photos that get uploaded to our albums. Amazon Simple Storage Service (S3) is a great option for this and Amplify's Storage module makes setting up and working with S3 very easy.

### Configuring and adding storage

First, we'll use the Amplify CLI to enable storage for our app. This will create a bucket on Amazon S3 and set it up with appropriate permissions so that users who are logged in to our app can read from and write to it. 

1. **From the photoalbums directory, run** `amplify add storage`

2. **Select 'Content (Images, audio, video, etc.)'** at the prompt

3. **Enter** `photoalbumsstorage` for the friendly category name
4. **accept default** for the bucket name

4. **Chose Auth users only** when asked who should have access. Configure it so that **authenticated users** have access with **create/update, read, and delete access** (use the spacebar to toggle on/off, the arrow keys to move, and Enter to continue).

5. **Select _No_** when asked to add a Lambda Trigger for your S3 Bucket. 

(Later, we will and add a Lambda function that will be triggered when a new photo is added. The function will create a thumbnail of the uploaded image and add EXIF data to our album.)

Here is sample output with responses:

```text
$ amplify add storage

? Please select from one of the below mentioned services:

Content (Images, audio, video, etc.)

? Please provide a friendly name for your resource that will be used to label this category in the project: photoalbumsstorage

? Please provide bucket name: <accept the default value>

? Who should have access: Auth users only

? What kind of access do you want for Authenticated users? 
◉ create/update
◉ read
◉ delete

? Do you want to add a Lambda Trigger for your S3 Bucket? N
```

Now we'll have Amplify modify our cloud environment, provisioning the storage resources we just added.

1. **Run** `amplify push` 
2. **Press Enter** to confirm the changes
3. Wait for the provisioning to finish. Adding storage usually only takes a minute or two.

### View the created storage in the Amplify console

![Amplify Storage](/images/amplify_storage.png)

![Amplify Storage](/images/amplify_storage_details.png)

{{% notice tip %}}
You can read more about Amplify's Storage module [here](https://aws-amplify.github.io/amplify-js/media/storage_guide).
{{% /notice %}}
