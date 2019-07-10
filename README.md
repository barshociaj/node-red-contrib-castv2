# node-red-contrib-castv2

A Node-Red node that provides basic Google Cast functionality based on the node-castv2-client package.

## Getting Started

Install directly from your NodeRED's Setting Pallete

or

This assumes you have [node-red](http://nodered.org/) already installed and working, if you need to install node-red see [here](http://nodered.org/docs/getting-started/installation)

```shell
$ cd ~/.node-red
$ npm install node-red-contrib-home-assistant
```
## Usage

This package provides a single node, `castv2-sender`, which will be under the "functions" group in the pallete. The node exposes a single configuration setting, the IP address / host name of the target cast device, but this can be left empty and you can supply `msg.host` on the incoming message as well if that's easier. The node will always output the current google cast device state after every command.

A `msg.appId` can also be specified if you'd like to launch or control an app other than the default media casting application, for instance to launch a custom cast receiver, etc. This is experimental.

General msg format expected on input is as follows:

```js
{
  host: "1.1.1.1", // optional if specified on the node itself
  appId: "", // optional, allows launching and controlling apps other than DefaultMediaReceiver
  payload: {
    type: "TYPE",
    ...
  }
}
```

The following commands are supported as `msg.payload.type`. Note `msg.payload` can be left `null` and on trigger the node will just output the current device status. Unless otherwise stated in the examples below, nothing else is necessary on the `msg.payload` except `msg.payload.type`.

| Command    | Example                                                          |
|------------|------------------------------------------------------------------|
| CLOSE      | Closes the current running application / cast session            |
| GET_STATUS | Gets status from current running application (not device)        |
| GET_VOLUME | Triggers a query to get current volume information (not used)    |
| MEDIA      | Load a single media file or queue multiple to play               |
| MUTE       | Mute cast device                                                 |
| PAUSE      | Pause current media                                              |
| PLAY       | Play current media                                               |
| SEEK       | Seek to time in current media                                    |
| STOP       | Stop playing current media without exiting application           |
| TTS        | Create a text-to-speech MP3 and cast to device                   |
| VOLUME     | Set volume of cast device                                        |
| UNMUTE     | Unmute cast device                                               |

#### MEDIA Example

```js
{
  host: "1.1.1.1",
  payload: {
    type: "MEDIA",
    media: {
      url: "http://test.com/media.mp3",
      contentType: "audio/mp3", // optional if type can be infered from url file type
      streamType: "BUFFERED", // optional unless you want to send LIVE instead
      title: "Media title", // optional unless desired
      image: "http://test.com/media.png" // optional image to pass to cast device for icon, etc.
    }
  }
}
```

Alternatively, you can send an array for `msg.payload.media` with a collection of objects of the same format to trigger loading a media queue to the cast device instead.

```js
{
  host: "1.1.1.1",
  payload: {
    type: "MEDIA",
    media: [
      { url: "http://test.com/media.mp3", ... },
      { url: "http://test.com/someOtherMedia.mp3", ... },
      ...
    ]
  }
}
```

#### TTS Example

```js
{
  host: "1.1.1.1",
  payload: {
    type: "TTS",
    text: "Something to say",
    speed: 1, // optional to adjust TTS speed, defaults to 1
    language: "en", // optional to set TTS language, default to en
    title: "Media title", // optional unless desired
  }
}
```

#### VOLUME Example

```js
{
  host: "1.1.1.1",
  payload: {
    type: "VOLUME",
    volume: 100 // 0 to 100
  }
}
```

#### SEEK Example

```js
{
  host: "1.1.1.1",
  payload: {
    type: "SEEK",
    time: 100 // Time to seek to in seconds
  }
}
```

## Known Issues
- SEEK is untested so far
- MEDIA queue is untested
- VOLUME, MUTE, and UNMUTE may or may not work correctly, hunting an issue on a SHIELD TV

