@ -0,0 +1,147 @@
OpenTok Cordova Basic Video Chat
=======================

This sample application shows how to connect to an OpenTok session, publish a stream, and subscribe to a stream in a basic Cordova iOS & Android application.

## Quick Start
1. Get values for your OpenTok API key, session ID, and token. You can obtain these values from your TokBox account. Make sure that the token isn't expired.
For testing, you can use a session ID and token generated at your TokBox account page. However, the final application should obtain these values using the [OpenTok server SDKs](https://tokbox.com/developer/sdks/server/). For more information, see the OpenTok developer guides on [session creation](https://tokbox.com/developer/guides/create-session/) and [token creation](https://tokbox.com/developer/guides/create-token/).

2. Clone this repo

3. ```$ cordova prepare```

4. ```$ npm install ```

## Getting an OpenTok session ID, token, and API key

An OpenTok session connects different clients letting them share audio-video streams and send
messages. Clients in the same session can include iOS, Android, and web browsers.

**Session ID** -- Each client that connects to the session needs the session ID, which identifies
the session. Think of a session as a room, in which clients meet. Depending on the requirements of
your application, you will either reuse the same session (and session ID) repeatedly or generate
new session IDs for new groups of clients.

*Important*: This demo application assumes that only two clients -- the local Web client and
another client -- will connect in the same OpenTok session. For sample purposes, you can reuse the
same session ID each time two clients connect. However, in a production application, your
server-side code must create a unique session ID for each pair of clients. In other applications,
you may want to connect many clients in one OpenTok session (for instance, a meeting room) and
connect others in another session (another meeting room).

**Token** -- The client also needs a token, which grants them access to the session. Each client is
issued a unique token when they connect to the session. Since the user publishes an audio-video
stream to the session, the token generated must include the publish role (the default). For more
information about tokens, see the OpenTok [Token creation
overview](https://tokbox.com/opentok/tutorials/create-token/).

**API key** -- The API key identifies your OpenTok developer account.

## Connecting to the session

First, this method initializes a Session object:
```
    // Set Credentials
    var apiKey = "";
    var sessionId = "";
    var token = "";
```

Upon obtaining the session ID, token, and API, we initialize the session.

  ``` 
    // Initialize Session Object
    var session = OT.initSession(apiKey, sessionId);
  ```

The `TB.initSession()` method takes two parameters -- the OpenTok API key and the session ID. It
initializes and returns an OpenTok Session object.

The `connect()` method of the Session object connects the client application to the OpenTok
session. You must connect before sending or receiving audio-video streams in the session (or before
interacting with the session in any way). The `connect()` method takes two parameters -- a token
and an optional completion handler function:
```
    // Connect to the Session
    session.connect(token, () => {
      
      // publish to the session
    });
```

The Session object dispatches a `streamDestroyed` event when the stream is destroyed. The application defines an event handler for this event:

``` session.on({
    streamDestroyed: (event) => {
      console.log(`Stream ${event.stream.name} ended because ${event.reason}`);
    }
  });
```

## Publishing an audio video stream to the session

Upon successfully connecting to the OpenTok session (see the previous section), the application
initializes an OpenTok Publisher object and publishes an audio-video stream to the session. This is
done inside the completion handler for the connect() method, since you should only publish to the
session once you are connected to it.

The Publisher object is initialized as shown below. The `TB.initPublisher()` method takes two
parameters:

* The API key
* The target DOM element or DOM element ID for placement of the publisher video
* A set of publisher properties (optional)

```
  var publisher = TB.initPublisher(apiKey, 'publisher');

```

Once the Publisher object is initialized, we publish to the session using the `publish()`
method of the Session object:

```
  session.publish(publisher);
```
## Subscribing to another client's audio-video stream

The Session object dispatches a `streamCreated` event when a new stream (other than your own) is
created in a session. A stream is created when a client publishes to the session. The
`streamCreated` event is also dispatched for each existing stream in the session when you first
connect. This event is defined by the StreamEvent object, which has a `stream` property,
representing stream that was created. The application adds an event listener for the
`streamCreated` event and subscribes to all streams created in the session using the
`session.subscribe()` method:

```
    // Subscribe to a newly created stream
    session.on({
      streamCreated: (event) => {
        session.subscribe(event.stream, 'subscriber', (error) => {
          console.log(`There was an error subscribing. Error: ${error}`);
        });
      }
    });
```
The `session.subscribe()` method takes four parameters:

* The Stream object to which we are subscribing to
* The target DOM element or DOM element ID (optional) for placement of the subscriber video
* A set of properties (optional) that customize the appearance of the subscriber view
* Optional Completion handler

## Running the application

# For Android: 
1. ```cordova prepare android```
2. Open project in Android Studio
3. Run

Note: If you're using the simulator, you will see a black container for your publisher since the simulator doesn't have a camera.

# For iOS
1. ```cordova prepare iOS```
2. Open the project in XCode
3. Sign the project
4. Run

Note: If you're using the simulator, you will see a simulation for your publisher since the simulator doesn't have a camera.
