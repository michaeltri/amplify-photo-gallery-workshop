+++
title = "Bootstrapping the App"
chapter = false
weight = 20
+++

### Creating a React app
We'll get things started by building a new React web app using the **create-react-app** CLI tool. 

{{% notice info %}}
This will give us a sample React app with a local auto-reloading web server and some helpful transpiling support for the browser like letting us use async/await keywords, arrow functions, and more.
{{% /notice %}}

{{% notice tip %}}
You can learn more about create-react-app at [https://github.com/facebook/create-react-app](https://github.com/facebook/create-react-app).
{{% /notice %}}

**In the Cloud9 terminal, run** `npx create-react-app photoalbums`.

**Then, navigate to the newly created directory with** `cd photoalbums`.


### Adding Semantic UI React

Before we start writing our UI, we'll also include Semantic UI components for React to give us components that will help make our interface look a bit nicer.

**In the photoalbums directory, run** 

```sh
npm install --save aws-amplify aws-amplify-react @reach/router uuid semantic-ui-react semantic-ui-css
```
Then, **edit src/index.js** and import the semantic-ui stylesheet:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import * as serviceWorker from './serviceWorker';

import 'semantic-ui-css/semantic.min.css'

ReactDOM.render(<App />, document.getElementById('root'));
... 
```

### Starting the App
Now let's start our development server so we can make changes and see them refreshed live in the browser.

**In the photoalbums directory, run** `npm start`. 

Once the web server has started, click the **Preview** menu and **select Preview Running Application**

![preview running application](/images/preview_running_application.png)

If you'd like, you can also **pop the preview to a new window**:

![pop app to new window](/images/pop_browser_new_window.png)

Finally, **open another terminal window**. We'll leave this first terminal alone since it's running the web server process.

![new terminal](/images/c9_new_terminal.png)
