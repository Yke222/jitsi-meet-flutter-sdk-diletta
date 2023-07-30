# Jitsi Meet Flutter SDK

A flutter plugin that serves as a Jitsi Meet flutter SDK.


## Installation

### Add dependency

Add this to the `pubspec.yaml` file in your project:

```yaml
dependencies:
    jitsi_meet_flutter_sdk: '^0.0.1'
```

### Install 

Install the packages from the terminal:

```bash
$ pub get
```

### Import files

Import the following files into your dart code:

```dart
import 'package:jitsi_meet_flutter_sdk/jitsi_meet.dart';
import 'package:jitsi_meet_flutter_sdk/jitsi_meet_conference_options.dart';
```

### Usage

#### Join meeting

Firstly, create a `JitsiMeet` object, then call the method `join` from it with a `JitsiMeetConferenceOptions` object

```dart
var jitsiMeet = JitsiMeet();
var options = JitsiMeetConferenceOptions(room: 'jitsiIsAwesome');
jitsiMeet.join(options);
```

## Configuration

### iOS

Make sure in `Podfile` from `ios` directory you set the ios version `12.4 or higher` 

```
platform :ios, '12.4'
```

The plugin requests camera and microphone access, make sure to include the required entries for `NSCameraUsageDescription` and `NSMicrophoneUsageDescription` in your `Info.plist` file from the `ios/Runner` directory.

```plist
<key>NSCameraUsageDescription</key>
<string>The app needs access to your camera for meetings.</string>
<key>NSMicrophoneUsageDescription</key>
<string>The app needs access to your microphone for meetings.</string>
```

### Android

Go to `android/app/build.gradle` and make sure that the `minSdkVersion` is set to `at lest 24`

```
android {
    ...
    defaultConfig {
        ...
        minSdkVersion 24
    }
}
```


The `application:label` field from the Jitsi Meet Android SDK will conflict with your application's one . Go to `android/app/src/main/AndroidManifest.xml` and add the tools library and `tools:replace="android:label"` to the application tag.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:tools="http://schemas.android.com/tools">
    <application
        tools:replace="android:label"
        android:label="sample_app"
        android:name="${applicationName}"
        android:icon="@mipmap/ic_launcher">
        ...
    </application>
</manifest>
```
## Using the API

### JitsiMeet

The `JitsiMeet` class is the entry point for the sdk. It is used to launch the meeting screen and to send all the events.

#### JitsiMeet()
The constructor for the class.


#### join(JitsiMeetConferenceOptions options, [JitsiMeetEventListener? listener])
Joins a meeting with the given options and optionally a listener is given

- `JitsiMeetConferenceOptions` options - meeting options
- `JitsiMeetEventListener` listener - event listener for events triggered by the native sdks

#### hangUp()

The localParticipant leaves the current meeting.

#### setAudioMuted({required bool muted})

Sets the state of the localParticipant audio muted according to the `muted` parameter.

#### setVideoMuted({required bool muted})
Sets the state of the localParticipant video muted according to the `muted` parameter.

#### sendEndpointTextMessage({String? to, required String message})
Sends a message via the data channel to one particular participant or to all of them. If the to param is empty, the message will be sent to all the participants in the conference.

In order to get the participantId, the `participantsJoined` event should be listened for, which have as a parameter the `participantId` and this should be stored somehow.

#### toggleScreenShare({required bool enabled})

Sets the state of the localParticipant screen sharing according to the `enabled` parameter.

#### openChat({String? to})

Opens the chat dialog. If `to` contains a valid participantId, the private chat with that particular participant will be opened.

#### sendChatMessage({String? to, required String message})

Sends a chat message via to one particular participant or to all of them. If the `to` param is empty, the message will be sent to all the participants in the conference.

In order to get the participantId, the `participantsJoined` event should be listened for, which have as a parameter the `participantId` and this should be stored somehow.

#### closeChat()

Closes the chat dialog.

#### retrieveParticipantsInfo()

Sends and event that will trigger the `participantsInfoRetrieved` events which will contain participants information


### JitsiMeetConferenceOptions

This object encapsulates all the options that can be tweaked when joining a conference.

Example:

```dart
var options = JitsiMeetConferenceOptions(
      room: "jitsiIsAwesomeWithFlutter",
      configOverrides: {
        "startWithAudioMuted": false,
        "startWithVideoMuted": false,
        "subject" : "Jitsi with Flutter",
      },
      featureFlags: {
        "unsaferoomwarning.enabled": false
      },
      userInfo: JitsiMeetUserInfo(
          displayName: "Flutter user",
          email: "user@example.com"
      ),
    );
```

All the values that can be added to the `configOverrides` can be found [here](hhttps://github.com/jitsi/jitsi-meet/blob/master/config.js).

All the values that can be added to the `featureFlags` can be found [here](https://github.com/jitsi/jitsi-meet/blob/master/react/features/base/flags/constants.ts).

#### JitsiMeetUserInfo({String displayName, String email, String avatar})
The constructor for the JitsiMeetUserInfo. 
P.S. the avatar should be an url.

### JitsiMeetEventListener

This class intends to be used as a listener for events that come from the native sdks. It will receive as arguments the event handlers

#### Function(String url) conferenceJoined

Called when a conference was joined.
- url: the conference URL

#### Function(String url, Object? error) conferenceTerminated

Called when the active conference ends, be it because of user choice or because of a failure.

- url: the conference URL
- error: missing if the conference finished gracefully, otherwise contains the error message

#### Function(String url) conferenceWillJoin

Called before a conference is joined.

- url: the conference URL

#### Function(String? email, String? name, String? role, String? participantId) participantJoined

Called when a participant has joined the conference.

- email: the email of the participant. It may not be set if the remote participant didn't set one.
- name: the name of the participant.
- role: the role of the participant.
- participantId: the id of the participant.

#### Function(String? participantId) participantLeft

Called when a participant has left the conference.

- participantId: the id of the participant that left.

#### Function(bool muted) audioMutedChanged

Called when the local participant's audio is muted or unmuted. 

- muted: a boolean indicating whether the audio is muted or not.

#### Function(bool muted) videoMutedChanged

Called when the local participant's video is muted or unmuted. 

- muted: an integer indicating whether the video is muted or not. 0 means unmuted, 4 means muted.

#### Function(String senderId, String message) endpointTextMessageReceived

Called when an endpoint text message is received.

- senderId: the participantId of the sender
- message: the content.

#### Function(String participantId, bool sharing) screenShareToggled

Called when a chat message is received.

- participantId: the id of the participant
- sharing: the state of screen share

#### Function(String senderId, String message, bool isPrivate)chatMessageReceived;

Called when a chat text message is received.

- senderId: the id of the participant that sent the message.
- message: the content of the message.
- isPrivate: true if the message is private, false otherwise.
- timestamp: the (optional) timestamp of the message.

#### Function(bool isOpen)? chatToggled

Called when the chat dialog is opened or closed.

- isOpen: true if the chat dialog is open, false otherwise.

#### Function() readyToClose

Called when the SDK is ready to be closed. No meeting is happening at this point.

Example of listener:

```dart
var listener = JitsiMeetEventListener(
      conferenceJoined: (url) {
        debugPrint("conferenceJoined: url: $url");
      },

      participantJoined: (email, name, role, participantId) {
        debugPrint(
          "participantJoined: email: $email, name: $name, role: $role, "
              "participantId: $participantId",
        );
        participants.add(participantId!);
      },

      chatMessageReceived: (senderId, message, isPrivate) {
        debugPrint(
          "chatMessageReceived: senderId: $senderId, message: $message, "
              "isPrivate: $isPrivate",
        );
      },

      readyToClose: () {
        debugPrint("readyToClose");
      },
    );
```











