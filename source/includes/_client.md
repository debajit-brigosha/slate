# Client

## Introduction

Welcome to the Brigosha multimedia client API! You can use our API to access Brigosha multimedia API server, which can enable you to make video/audio calls , share screen and stream recorded video files  on various platforms.


## Create Client 

```javascript
 rc = new RoomClient('room_name', 'user_name', callback);
```

> The above command creates and intialize a client instance:


This code creates and initializes the client instance.


### Function Parameters

Parameter | Description
--------- | -----------
room_name | clients can communicate with a common room name
user_name | name of the user joining the streaming group
callback | functions that will be called after creating a client instance

## Create Room 

```javascript
 async createRoom(room_id) {
    await socket
      .request('createRoom', {
        room_id,
      })
      .catch((err) => {
        console.log(err);
      });
  }
```

> The above command creates a room 


This code creates a room.


### Function Parameters

Parameter | Description
--------- | -----------
room_name | clients can communicate with a common room name

## Join Room 

```javascript
async join(name, room_id) {
    socket
      .request('join', {
        name,
        room_id,
      })
      .then(
       ............
        },
      )
      .catch((e) => {
        console.log('failed to join ', e);
      });
  }
```

> The above command joins the user to the room specified


This code joins the user to a room.


### Function Parameters

Parameter | Description
--------- | -----------
name | name of the user who joins the room
room_id | name of the room  which the user wants to join

## Websocket Listeners

### Consumer Closed

```javascript
socket.on(
      'consumerClosed',
      function ({consumer_id}) {
        ........
        this.removeConsumer(consumer_id);
      }.bind(this),
    );
```

> The above command triggers when a consumer is closed


websocket event when a consumer is closed.


### Function Parameters

Parameter | Description
--------- | -----------
consumer_id | id of the remote stream subscriber

### New Producer Found

```javascript
socket.on(
      'newProducers',
      async function (data) {
        for (let {producer_id, producer_socket_id} of data) {
          await this.consume(producer_id, producer_socket_id);
        }
      }.bind(this),
    );
```

> The above command triggers when a client start audio or video stream


websocket event when a new stream received.


### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of the remote producer stream
producer_socket_id | id of the socket of remote producer 

### Socket Disconnect 

```javascript
socket.on(
      'disconnect',
      function () {
        this.exit(true);
      }.bind(this),
    );
```

> The above command triggers when a client is disconnected from server


websocket event when a client disconnects.

### Existing Online Users

```javascript
 socket.on(
      'userList',
      function (data) {
        this.userList = data;
      }.bind(this),
    );
```

> The above command triggers when a client joins a room 


websocket event to get all the online users.


### Function Parameters

Parameter | Description
--------- | -----------
userList | array of users having username and their socket id

 

### New User

```javascript
 socket.on(
      'newUserJoined',
      function (data) {
        this.userList.push(data);
      }.bind(this),
    );
```

> The above command triggers when another new client joins a room 


websocket event to get details of new user in the room.


### Function Parameters

Parameter | Description
--------- | -----------
username | the name of the user who joins the room
user_socket_id | socket id of the user to send unicast messages

### User Left

```javascript
   socket.on(
      'userLeft',
      function (data) {
        this.userList = this.userList.filter(
          (user) => user.user_socket_id !== data.user_socket_id,
        );
      }.bind(this),
    );
```

> The above command triggers when client leaves a room 


websocket event to get details of user in the room who leaves the room.
`only user_socket_id shall be received in this case`

### Function Parameters

Parameter | Description
--------- | -----------
user_socket_id | socket id of the user to send unicast messages

### Common Event Notification

```javascript
  socket.on(
      'eventNotification',
      function (data) {
        this.eventNotification.push(data);
      }.bind(this),
    );
```

> The above command triggers when server sends activity notification , e.g - userJoined, userLeft ,audioStarted, ...


websocket event to display activity notification from server.

### Function Parameters

Parameter | Description
--------- | -----------
activity | name of the activity , e.g - start, pause, resume, joined, left, disconnected
user | name of the user who triggered the event 
option | optional data can be displayed

### Active Speaker

```javascript
 socket.on(
      'activeSpeaker',
      function (data) {
        this.activeSpeaker = this.remoteMedia.filter(
          (media) => media.audio && media.audio.producer_id === data.producerId,
        );
        this.activeSpeaker =
          this.activeSpeaker.length === 0
            ? {userName: 'Me'}
            : this.activeSpeaker[0];
      }.bind(this),
    );
```

> The above command triggers in a time interval and receives the active speaker from the server


websocket event to display active speaker.

### Function Parameters

Parameter | Description
--------- | -----------
producerId | id of the producer that has the highest noise (active speaker)
sound  | noise level in dB ( from -127 to 0, -80 is default)

### Pause Remote audio/video

```javascript
socket.on(
      'pauseProducerRemoteRcv',
      function (producer_id) {
        if (this.producers.has(producer_id)) {
          this.producers.get(producer_id).pause();
        } else {
          console.log('not my producer to pause');
        }
      }.bind(this),
    );
```

> The above command triggers when a remote user requests to pause stream.


websocket event to pause stream.

### Function Parameters

Parameter | Description
--------- | -----------
producerId | id of the producer for which the pause request is made.

### Resume Remote audio/video

```javascript
 socket.on(
      'resumeProducerRemoteRcv',
      function (producer_id) {
        if (this.producers.has(producer_id)) {
          this.producers.get(producer_id).resume();
        } else {
          console.log('not my producer to resume');
        }
      }.bind(this),
    );
```

> The above command triggers when a remote user requests to resume stream.


websocket event to resume stream.

### Function Parameters

Parameter | Description
--------- | -----------
producerId | id of the producer for which the resume request is made.


## Server Configuration

```javascript
 const data = await socket.request('getRouterRtpCapabilities');
```

> The above command receives the media configuration from server


get the configuration of media codec

### Function Parameters

Parameter | Description
--------- | -----------
getRouterRtpCapabilities | configuration object

## Register WebRTC

```javascript
 registerGlobals();
```

> WebRTC  RTCPeerConnection, RTCIceCandidate, RTCSessionDescription initialization


Initialize WebRTC  RTCPeerConnection, RTCIceCandidate, RTCSessionDescription

## Create Device Type 

```javascript
   try {
      device = new mediasoupClient.Device({handlerName: 'ReactNative'});
    } catch (error) {
      if (error.name === 'UnsupportedError') {
        console.error('Device not supported');
      }
      console.error('failed to load device', error);
    }
```

> Create a device instance to record audio , video 


Select and initialize client device type

### Function Parameters

Parameter | Description
--------- | -----------
ReactNative | platform name of the handler

## Matches Media Configuration

```javascript
  const data = await socket.request('createWebRtcTransport', {
        forceTcp: false,
        rtpCapabilities: device.rtpCapabilities,
      });
```

> Send device media configuration to the server and receives server media configuration


Request the server to send a matching media configuration

### Function Parameters

Parameter | Description
--------- | -----------
forceTcp | client used tcp over udp to communicate
rtpCapabilities | media configuration of the client device

## Create sendTransport

```javascript
  this.producerTransport = device.createSendTransport(data);
```

> Create a communinication layer between client and server


The above command creates a layer of communication between client and server

### Function Parameters

Parameter | Description
--------- | -----------
data | matching media configuration  after handshake

## Create receiveTransport

```javascript
  this.consumerTransport = device.createRecvTransport(data);
```

> Create a communinication layer between client and server to receive


The above command creates a layer of communication between client and server to receive remote streams

### Function Parameters

Parameter | Description
--------- | -----------
data | matching media configuration  after handshake

## Create or Produce

```javascript
   async produce(type) {
    .........
    switch (type) {
      case mediaType.audio:
        mediaConstraints = {
          audio: true,
          video: false,
        };
        audio = true;
        break;
      case mediaType.video:
        mediaConstraints = {
          audio: false,
          video: {
            width: {
              min: 640,
              ideal: 640,
            },
            height: {
              min: 400,
              ideal: 400,
            },
            ........
```

> This function creates a local audio or video stream by using microphone or camera


Get the stream from microphone or camera and produce as a steam.

### Function Parameters

Parameter | Description
--------- | -----------
min | minimum resolution of the video 
ideal | start the video with this resolution


## Receive  or Consume

```javascript
  async consume(producer_id, producer_socket_id) {
    this.getConsumeStream(producer_id, producer_socket_id).then(
      function ({consumer, stream, kind, userName}) {
        this.consumers.set(consumer.id, consumer);
        ........
```

> This function receives the remote stream from server


Get the remote stream from server that is sent from different client.

### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of producer which is the sending stream
producer_socket_id | socket id at the producer's end

## Mute Local

```javascript
 this.producers.get(producer_id).pause();
```

> This function mutes the local stream


Mute local stream, stops sending the data but connection persists

### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of producer which is the sending stream

## unMute Local

```javascript
  this.producers.get(producer_id).resume();
```

> This function unMutes the local stream


Plays or resume local stream, starts sending the data.

### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of producer which is the sending stream

## Mute Remote

```javascript
 pauseProducerRemote(producer_id) {
    socket.request('pauseProducerRemote', {producer_id});
  }
```

> This function Mutes the remote stream


Pause or mute remote stream, stops remote client to send data.

### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of producer which is the sending stream

## unMute Remote

```javascript
 pauseProducerRemote(producer_id) {
    socket.request('resumeProducerRemote', {producer_id});
  }
```

> This function unMutes the remote stream


plays or unmute remote stream, starts remote client to send data.

### Function Parameters

Parameter | Description
--------- | -----------
producer_id | id of producer which is the sending stream
