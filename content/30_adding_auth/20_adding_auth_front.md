+++
title = "Rendering the Front End"
chapter = false
weight = 20
+++

Now that we have our backend set up for managing registrations and sign-in, all we need to do is use the _withAuthenticator_ [higher-order React component from AWS Amplify](https://aws-amplify.github.io/amplify-js/media/authentication_guide.html#using-components-in-react) to wrap our existing _App_ component. This will take care of rendering a simple UI for letting users sign up, confirm their account, sign in, sign out, or reset their password.

Add a new file in the src directory named **src/useAmplifyAuth.js**. Copy and paste the following code into the new file.

```js
import { useReducer, useState, useEffect } from 'react';
import { Auth, Hub } from 'aws-amplify';

const initalState = {
  isLoading: true,
  error: false,
  user: null
}

function reducer(state, action) {
  switch(action.type) {
    case 'init':
      return { ...state, isLoading: true, error: false }
    case 'success':
      return { ...state, isLoading: false, error: false, user: action.user }
    case 'reset':
      return { ...state, user: null }
    case 'error':
      return { ...state, isLoading: false, error: true }
    default:
      new Error();
  }
}

function useAmplifyAuth() {
  const [state, dispatch] = useReducer(reducer, initalState);
  const [fetchTrigger, setFetchTrigger] = useState(false);

  useEffect(() => {
    let isMounted = true;

    const fetchUser = async () => {
      if (isMounted) {
        dispatch({ type: 'init' });
      }

      try {
        if (isMounted) {
          const authData = await Auth.currentUserInfo();
          if (authData) {
            dispatch({ type: 'success', user: authData });
          }
        }
      } catch (error) {
        if (isMounted) {
          console.error('[ERROR - useAmplifyAuth]', error);
          dispatch({ type: 'error' });
        }
      }
    };

    const HubListener = () => {
      Hub.listen('auth', data => {
        const { payload } = data;
        onAuthEvent(payload);
      });
    };

    const onAuthEvent = (payload) => {
      switch(payload.event) {
        case 'signIn':
          // on signin, we want to rerun effect, trigger via flag
          if (isMounted) { setFetchTrigger(true); }
          break;
        default:
          // ignore anything else
          return;
      }
    };

    HubListener();
    fetchUser();

    // on tear down...
    return () => {
      Hub.remove('auth');
      isMounted = false;
    }
  }, [fetchTrigger]);

  const onSignOut = async () => {
    try {
      await Auth.signOut();
      setFetchTrigger(false);
      dispatch({ type: 'reset' })
    } catch (error) {
      console.error('[ERROR - useAmplifyAuth]', error);
    }
  };

  return { state, onSignOut };
}

export default useAmplifyAuth;
```

Open **src/App.js** and replace its contents with the following

```js
import React from 'react';
import { Container, Menu } from 'semantic-ui-react';
import { Router, Link } from "@reach/router";

import { withAuthenticator } from 'aws-amplify-react';
import useAmplifyAuth from './useAmplifyAuth';

export const UserContext = React.createContext();

function App() {
  const { state: { user }, onSignOut } = useAmplifyAuth();

  function UserData(props) {
    return !user ? (
      <div></div>
    ) : (
      <div>Welcome {user.username} (<Link to="/" onClick={onSignOut}>Sign Out</Link>)</div>
    );
  }

  return (
    <div>
      <Menu fixed='top' borderless inverted>
        <Container>
          <Menu.Item as={Link} to='/' header>
            Amplify Photo Album
          </Menu.Item>

          <Menu.Menu position='right'>
            <Menu.Item>
              <UserData></UserData>
            </Menu.Item>
          </Menu.Menu>
        </Container>
      </Menu>

      <Container text style={{ marginTop: '5em' }}>
        <UserContext.Provider user={ user }>
          <p>To be updated...</p>
        </UserContext.Provider>
      </Container>    
    </div>
  );
}

export default withAuthenticator(App, { signUpConfig: { hiddenDefaults: ['phone_number'] } });
```

Take a look at the web app now and you should have a sign-up / sign-in form!

```
npm start
```

It is safe to ignore the no-unused-vars warning.


### What we changed in App.js

- Imported and configured the AWS Amplify JS library

- Imported the withAuthenticator higher order component from aws-amplify-react

- Wrapped the App component using withAuthenticator

### Creating an account

**Create an account in the app** by providing a username, password, and a **valid email address** (to receive a confirmation code at).

{{% notice info %}}
You'll be taken to a screen asking you to confirm a code. This is because Amazon Cognito wants to verify a user's email address before it lets them sign in. 
{{% /notice %}}

**Check your email**. You should have received a confirmation code message. **Copy and paste the confirmation code** into your app and you should then be able to log in with the username and password you entered during sign up. 

Once you sign in, the form disappears and you can see our App component rendered below a header bar that contains your username and a 'Sign Out' button.

{{% notice tip %}}
This is a pretty simple authentication UI, but there's a lot you can do to customize it, including replacing parts with your own React components or using a completely hosted UI that can redirect back to your app. See the Customization section of the [AWS Amplify Authentication Guide](https://aws.github.io/aws-amplify/media/authentication_guide#customization) for more information.
{{% /notice %}}

### Checkin and push the changes

```
git status
```

```
git add amplify/backend/auth
git add src/useAmplifyAuth.js
```

```
git commit -am 'added authentication'
```

```
git push origin master
```

#### todo add screen amplify build started