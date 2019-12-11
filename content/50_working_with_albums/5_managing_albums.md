+++
title = "Managing Albums"
chapter = false
weight = 5
+++

At this point, we have a web app that authenticates users and a secure GraphQL API endpoint that lets us create and read Album data. It's time to connect the two together!

{{% notice note %}}
As we saw above, [AWS Amplify](https://aws.github.io/aws-amplify/) is an open source JavaScript library that makes it very easy to integrate a number of cloud services into your web or React Native apps. We'll start by using its [Connect React component](https://aws-amplify.github.io/docs/js/api#connect) to take care of automatically querying our GraphQL API and providing data for our React components to use when rendering.
<br/><br/>
The Amplify CLI has already taken care of making sure that our *src/aws-exports.js* file contains all of the configuration we'll need to pass to the Amplify JS library in order to talk to the AppSync API. All we'll need to do is add some new code to interact with the API.
{{% /notice %}}

Here's what it will look like when we render our list of Albums:

![Rendering a list of albums in our app](/images/app-albums-screen.png?classes=border)

### Generate GraphQL queries, mutations, event handlers

```
amplify add codegen
```

```
? Choose the code generation language target javascript
? Enter the file name pattern of graphql queries, mutations and subscriptions src/graphql/**/*.js
? Do you want to generate/update all possible GraphQL operations - queries, mutations and subscriptions Yes
? Enter maximum statement depth [increase from default if your schema is deeply nested] 5
✔ Generated GraphQL operations successfully and saved at src/graphql
```

### Updating our App

Let's update our front-end to:
- allow users to create albums
- show a list of albums
- allow users to click into an album to view its details

**Replace photoalbums/src/App.js** with the following updated version:
<div style="height: 660px; overflow-y: scroll;">
{{< highlight jsx "hl_lines=5 6 8 9 14-207">}}
// src/App.js

import React from 'react';
import { Container, Menu } from 'semantic-ui-react';
import { Router, Link } from "@reach/router";

import { withAuthenticator } from 'aws-amplify-react';
import useAmplifyAuth from './useAmplifyAuth';
import Albums from './Albums';
import AlbumDetail from './AlbumDetail';

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
          <Router>
            <Albums path='/' user={ user } />
            <AlbumDetail path='/album/:albumId' user={ user } />
          </Router>
        </UserContext.Provider>
      </Container>    
    </div>
  );
}

export default withAuthenticator(App, { signUpConfig: { hiddenDefaults: ['phone_number'] } });
{{< /highlight >}}
</div>


**Add a new file  photoalbums/src/Albums.js** with the code:
<div style="height: 660px; overflow-y: scroll;">
{{< highlight jsx "hl_lines=5 6 8 9 14-207">}}
// src/Albums.js

import React, { useEffect, useReducer } from 'react';
import { Button, Form, Grid, Header, Input } from 'semantic-ui-react';
import { API, graphqlOperation } from 'aws-amplify';
import { Link } from "@reach/router";

import { listAlbums as listAlbumsQuery } from './graphql/queries';
import { createAlbum as createAlbumMutation } from './graphql/mutations';
import { onCreateAlbum } from './graphql/subscriptions';

const initalState = {
  albums: [],
  error: null,
  // form inputs for new album...
  newAlbumName: ''
};

function reducer(state, action) {
  switch(action.type) {
    case 'set':
      return { ...state, albums: action.albums }
     case 'add':
       return { ...state, albums: [ ...state.albums, action.album ] }      
    case 'input':
     return { ...state, [action.inputValue]: action.value }  
    case 'reset':
      return { ...state, [action.inputValue]: '' }
    case 'error':
      return { ...state, error: true }
    default:
      new Error();
  }
}

async function listAlbums(dispatch) {
  try {
    const albumsData = await API.graphql(graphqlOperation(listAlbumsQuery));
    dispatch({ type: 'set', albums: albumsData.data.listAlbums.items })
  } catch(error) {
    dispatch({ type: 'error' });
    console.error('[ERROR - listAlbums] ', error);
  }
}

async function createAlbum(user, state, dispatch) {
   const { newAlbumName } = state;
   const newAlbum = {
     name: newAlbumName,
     owner: user.username,
     ownerId: user.id
   }

   try {
     await API.graphql(graphqlOperation(createAlbumMutation, { input: newAlbum }));
     dispatch({ type: 'reset' });
     console.log('New album created');
   } catch (error) {
     dispatch({ type: 'error' });
     console.error('[ERROR - createAlbum] ', error);
   }    
}

function update(value, inputValue, dispatch) {
    dispatch({ type: 'input', value, inputValue });
}

function AlbumList(props) {
  return (
    <ul>
      {
        props.albums.map((album, i) => (
          <li key={i}>
            <Link to={'/album/' + album.id}>{album.name}</Link>
          </li>
        ))
      }
    </ul>
  );
}

function Albums({ user }) {
  const [state, dispatch] = useReducer(reducer, initalState);
   useEffect(() => {
      if (!user) { return; }
        const { username } = user;
        const subscription = API.graphql(graphqlOperation(onCreateAlbum, { owner: username })).subscribe({
           next: (data) => {
             const album = data.value.data.onCreateAlbum;
             dispatch({ type: 'add', album });
           }
         });
 
     return () => {
       subscription.unsubscribe();
     }
   }, [user]);

  useEffect(() => {
    listAlbums(dispatch)
  }, []);

  return (
    <div>
      <Header as='h1'>My Albums</Header>

      <Grid divided>
        <Grid.Row>
          <Grid.Column width={8}>
            <AlbumList albums={state.albums} />
          </Grid.Column>

          <Grid.Column width={4}>
            <Header as='h3'>Create Album</Header>

            <Form>
              <Form.Field>
                <label>Name</label>
                <Input placeholder='name'
                  onChange={ e => update(e.target.value, 'newAlbumName', dispatch) }
                  value={ state.newAlbumName } />
              </Form.Field>
              <Button primary onClick={() => createAlbum(user, state, dispatch)}>
                Create
              </Button>
            </Form>
          </Grid.Column>
        </Grid.Row>
      </Grid>
    </div>
  )
};

export default Albums;

{{< /highlight >}}
</div>

**Add a new file  photoalbums/src/AlbumDetail.js** with the code:
<div style="height: 660px; overflow-y: scroll;">
{{< highlight jsx "hl_lines=5 6 8 9 14-207">}}
// src/AlbumDetail.js

import React, { useEffect, useState, useReducer } from 'react';
import { Button, Card, Header, Icon, Image, Message, Modal } from 'semantic-ui-react';
import { API, Storage, graphqlOperation } from 'aws-amplify';
import { S3Image, PhotoPicker } from 'aws-amplify-react';
import awsconfig from './aws-exports';
import uuid from 'uuid/v4';

import { getAlbum as getAlbumQuery } from './graphql/queries';
import { createPhoto as createPhotoMutation } from './graphql/mutations';

function PhotoCard(props) {
  const [src, setSrc] = useState('');
  const { fullsize } = props.photo;

  return (
    <Card>
      <S3Image hidden level='protected' imgKey={ fullsize.key } onLoad={ url => setSrc(url) } />
      <Image src={ src } />
    </Card>
  );
}

function PhotoGrid(props) {
  return (
    <Card.Group itemsPerRow={3}>
      {
        props.photos.map((photo, i) => (
          <PhotoCard key={ i } photo={ photo } />
        ))
      }
    </Card.Group>
  );
}

function AlbumDetail({ albumId, user }) {
  const {
    aws_user_files_s3_bucket_region: region,
    aws_user_files_s3_bucket: bucket
  } = awsconfig;
  
  const initalState = {
    album: {},
    photos: [],
    isLoading: false,
    message: '',
    error: null
  };

  const [openModal, showModal] = useState(false);
  const [state, dispatch] = useReducer(reducer, initalState);

  // add subscription for new photos in this album

  useEffect(() => {
    dispatch({ type: 'init' });
    getAlbum(albumId, dispatch);
  }, [albumId]);
  
  function reducer(state, action) {
    switch(action.type) {
      case 'init':
        return { ...state, isLoading: true };
      case 'set':
        return { 
          ...state,
          isLoading: false,
          album: action.album,
          photos: action.album.photos.items ? action.album.photos.items : []
        };
      case 'message':
        return { ...state, message: action.message };
      case 'error':
        return { ...state, error: true };
      default:
        new Error();
    }
  }
  
  async function getAlbum(albumId, dispatch) {
    try {
      const albumData = await API.graphql(graphqlOperation(getAlbumQuery, { id: albumId }));
      dispatch({ type: 'set', album: albumData.data.getAlbum });
    } catch (error) {
      dispatch({ type: 'error' });
      console.error('[ERROR - getAlbum] ', error);
    } 
  }
  
  async function createPhoto(data, state, dispatch) {
    if (data && data.file) {
      const { file, type: mimeType } = data;
      const extension = file.name.substr((file.name.lastIndexOf('.') + 1));
      const photoId = uuid();
      const key = `images/${photoId}.${extension}`;
  
      const inputData = {
        id: photoId,
        photoAlbumId: state.album.id,
        contentType: mimeType,
        fullsize: {
          key: key,
          region: region,
          bucket: bucket
        }
      };
  
      try {
        await Storage.put(key, file, { level: 'protected', contentType: mimeType, metadata: { albumId: state.album.id, photoId } });
        await API.graphql(graphqlOperation(createPhotoMutation, { input: inputData }));
        console.log(`Successfully created photo - ${photoId}`);
        dispatch({ type: 'message', message: 'New photo created successfully' });
        showModal(false);
      } catch(error) {
        console.error('[ERROR - createPhoto] ', error);
      }
    }
  }

  return state.isLoading ? (
    <p>loading...</p>
  ) : (
    <div>
      <Modal size='small' closeIcon
             open={openModal}
             onClose={() => { showModal(false) }}
             trigger={<Button primary floated='right' onClick={() => {showModal(true) }}>Add Photo</Button>}>
        <Modal.Header>Upload Photo</Modal.Header>
        <Modal.Content>
          <PhotoPicker preview onPick={(data) => createPhoto(data, state, dispatch)} />
        </Modal.Content>
      </Modal>
    
      <Header as='h1'>{ state.album.name }</Header>
      { state.message &&
        <Message><p>{ state.message }</p></Message> }
      <PhotoGrid photos={ state.photos } />
    </div>
  );
}

export default AlbumDetail;

{{< /highlight >}}
</div>

### Check-in and push your changes

```
git status
```

```
git add .graphqlconfig.yml
git add amplify/backend/api/
git add amplify/backend/storage/
git add src/AlbumDetail.js
git add src/Albums.js
git add src/graphql/
```

```
git commit -am 'manage albums and upload photos'
```

```
git push origin master
```

### Try out the app

Check out the app now and try out the new features: 

- View the list of albums

- Create a new album and see it appear in the albums list

- Click into an album to see the beginnings of our Album details view

- When viewing an Album, click 'Back to Albums list' to go home

{{% notice tip %}}
The loading magic here comes from [AWS Amplify's *Connect* component](https://aws-amplify.github.io/docs/js/api#connect) (which we imported from the *aws-amplify-react* package). All we need to do is pass this component a GraphQL query operation in its query prop. It takes care of running that query when the component mounts, and it passes information down to a child function via the data, loading, and errors arguments. We use those values to render appropriately, either showing some loading text or passing the successfully fetched data to our *AlbumsList* component.
{{% /notice %}}

{{% notice info %}}
The *listAlbums* query we're above using passes in a very high limit argument. This is because we can just load all of the albums in one request and sort the albums alphabetically on the client-side (instead of dealing with paginated DynamoDB responses). This keeps the *AlbumsList* code pretty simple, so it's probably worth the trade off in terms of performance or network cost.
{{% /notice %}}

{{% notice info %}}
Also worth noting is how we're leveraging an AppSync real-time subscription to automatically refresh the list of albums whenever a new album is created.
<br/>
<br/>
Our GraphQL schema contains a *Subscription* type with a bunch of subscriptions that were auto-generated back when we had AWS AppSync create the resources (like the DynamoDB table and the AWS AppSync resolvers) for our *Album* type. One of these the _onCreateAlbum_ subscription.
<br/>
<br/>
The _subscription_ and _onSubscriptionMsg_ properties on the _Connect_ component tell it to subscribe to the _onCreateAlbum_ event data and update the data for AlbumsList accordingly. 
<br/>
<br/>
The content for the subscription property looks very similar to what we provided for the query property previously; it just contains a query specifying the subscription we want to listen to and what fields we'd like back when new data arrives. The only slightly tricky bit is that we also need to define a handler function to react to new data from the subscription, and that function needs to return a new set of data that the _Connect_ component will use to refresh our _ListAlbums_ component. This is what we've done above.
{{% /notice %}}
