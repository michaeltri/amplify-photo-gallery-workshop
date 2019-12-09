+++
title = "Initializing AWS Amplify"
chapter = false
weight = 10
+++

{{% notice info %}}
The AWS Amplify CLI makes it easy for us to add cloud capabilities to our web and mobile apps, with SDKs available for React and React Native, iOS, and Android. To get started, we'll create a new application and enable user authentication. We'll then wire things up in our app using the open-source [AWS Amplify](https://aws-amplify.github.io/) JavaScript library, which the AWS Amplify CLI will take care of configuring for us; all we have to do is use it in our React app. AWS Amplify contains some nice abstractions for working with cloud services, and it has some helpful React components we'll use in our app.
{{% /notice %}}



### Initializing Amplify

**On the command line, in the photoalbums directory**:

1. **Be sure to be in photoalbums directory** `cd photoalbums`

1. **Run** `amplify init`

1. **Press _Enter_** to accept the default project name 'photoalbums'

1. **Enter 'master'** for the environment name

1. **Select 'None'** for the default editor (we're using Cloud9)

1. **Choose JavaScript and React** when prompted

1. **Accept the default values** for paths and build commands

1. **Select the default profile** when prompted

![amplify init](/images/amplify_init.png)

### todo add amplify status cli output

### todo show amplify console

### todo show s3 deployment bucket

### todo show cloudformation

### Configure the React application to use Amplify

**Edit src/index.js** and import and configure Amplify:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

import 'semantic-ui-css/semantic.min.css'
import Amplify from 'aws-amplify'
import awsconfig from './aws-exports'
Amplify.configure(awsconfig)

ReactDOM.render(<App />, document.getElementById('root'));
... 
```

