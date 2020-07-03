---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  # - shell
  # - ruby
  # - python
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/slatedocs/slate'>Documentation Powered by Brigosha</a>

includes:
  - client
  - errors
  

search: true

code_clipboard: true
---

# Introduction

Welcome to the Brigosha multimedia API! You can use our API to access Brigosha multimedia API endpoints, which can enable you to make video/audio calls , share screen and stream recorded video files  on various platforms.

Our api is written in JavaScript! You can view code examples in the dark area to the right.

# Authentication // **TODO

> To authorize, use this code:

```javascript
const lib = require('brigosha_multimedia');

let client = lib.authorize('apiKey');
```

> Make sure to replace `apiKey` with your API key.

<!-- Kittn uses API keys to allow access to the API. You can register a new Kittn API key at our [developer portal](http://brigosha.com/developers). -->

endpoint expects for the API key to be included in all API requests to the server in a header that looks like the following:

`Authorization: apiKey`

<aside class="notice">
You must replace <code>apiKey</code> with your personal API key.
</aside>

# Server

## create worker

```javascript
const mediasoup = require('mediasoup')

    for (let i = 0; i < numWorkers; i++) {
        let worker = await mediasoup.createWorker({
            logLevel: config.mediasoup.worker.logLevel,
            logTags: config.mediasoup.worker.logTags,
            rtcMinPort: config.mediasoup.worker.rtcMinPort,
            rtcMaxPort: config.mediasoup.worker.rtcMaxPort,
        })

        worker.on('died', () => {
            console.error('mediasoup worker died, exiting in 2 seconds... [pid:%d]', worker.pid);
            setTimeout(() => process.exit(1), 2000);
        })
    }
```

> The above code creates a thread on each core of the CPU


This should create threads on each core of the CPU.

### RUN

`code executes at server startup`

## Create Router 

```javascript
worker.createRouter({
            mediaCodecs
        }).then(async function (router) {
            this.router = router;
            console.log('router created');
            .........
        }
```

This function creates a router to route media stream  between specific clients.

<aside class="warning">To create a router you must pass the media codec configuration<code>&lt;
      router: {
        mediaCodecs: 
          [
            {
              kind: 'audio',
              mimeType: 'audio/opus',
              clockRate: 48000,
              channels: 2
            },
            {
              kind: 'video',
              mimeType: 'video/VP8',
              clockRate: 90000,
              parameters:
                {
                  'x-google-start-bitrate': 1000
                }
            },
          ]
      },&gt;</code></aside>


## Create Transport 

```javascript
 const transport = await this.router.createWebRtcTransport({
            listenIps: config.mediasoup.webRtcTransport.listenIps,
            enableUdp: true,
            enableTcp: true,
            preferUdp: true,
            initialAvailableOutgoingBitrate,
        });
```

> The above command establishes a communication between server and client to transport media streams:


This code initializes the communication that happens between clients.


### Function Parameters

Parameter | Description
--------- | -----------
listenIps | listenIps are the IP addresses of the server so that clents can send media strem to the server
enableUdp | uses UDP protocol
enableTcp | uses TCP protocol
preferUdp | uses Udp by default 
initialAvailableOutgoingBitrate | bitrate that is configured by defuault as server supported bitrate

## Produce Media

```javascript
async produce(socket_id, producerTransportId, rtpParameters, kind) {
        return new Promise(async function (resolve, reject) {
            let producer = await this.peers.get(socket_id).createProducer(producerTransportId, rtpParameters, kind)
            resolve(producer.id)
            .......
        }
}
```

> The above command produces a new media stream and transmit it over to all clients:


This code produces audio or video stram and starts sending.


### Function Parameters

Parameter | Description
--------- | -----------
socket_id | socket id of the client that is producing the stream
producerTransportId | transport id of the producer 
rtpParameters | media codec configuration 
kind | type of stream , e.g- audio/video

## Consume Media

```javascript
 async consume(socket_id, consumer_transport_id, producer_id,  rtpCapabilities) {
      
        let {consumer, params} = await this.peers.get(socket_id).createConsumer(consumer_transport_id, producer_id, rtpCapabilities)
        ............
    }
```

> The above command creates a consumer connection to stream and transmit it over to that client:


This code creates consumer connection of audio or video stram and starts transmitting.


### Function Parameters

Parameter | Description
--------- | -----------
socket_id | socket id of the client that is receiving the stream
consumer_transport_id | transport id of the receiver 
rtpParameters | media codec configuration 
producer_id | id of the client that is sending the stream
rtpCapabilities | media codec configuration of the client

## Active Speaker Detection

```javascript
   this.audioLevelObserver = await router.createAudioLevelObserver(
                {
                  maxEntries : 1,
                  threshold  : -80,
                  interval   : 2000
                });
```

> The above command creates a active speaker detector listener:


This code creates a listener that will detect the active speaker.

### Function Parameters

Parameter | Description
--------- | -----------
maxEntries | number of active speaker detected
threshold | voice detected in dB  
interval | listens in timeinterval in millisecond

## Notification Event

```javascript
   room.broadCast(socket.id,'eventNotification', {user: name, action, option})
```

> The above command sends notification to clients:


This code sends notification to clients.

Parameter | Description
--------- | -----------
socket.id | socket id of client wich generates the event, notification will not be sent to that client.
eventNotification | name of the event   
user | name of the user who generates the event or message
action | what type of event is triggered
option | optional message that can be sent such that description or warning