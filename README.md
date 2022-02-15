# React Native Brightcove Player


- A React Native implementation of Brightcove Player SDK.
- Supported Features
  - Brightcove player component
  - Poster image component
  - Retrieving playlist
  - Managing offline playback with Dynamic Delivery

## Supported Version

- iOS 11 >=
- Android 5.0 >=
- Brightcove SDK 6.x

## Installation

```console
npm install <path/to>/react-native-brightcove-player-1.7.0.tgz
```

### iOS

- Add source to `Podfile` and `pod install && pod update`
- Specify platform version to `11.0`

```rb
source 'https://github.com/brightcove/BrightcoveSpecs.git'
source 'https://github.com/CocoaPods/Specs'

platform :ios, '10.0'
```
Once installation is completed, open project_name.xcworkspace file from ios directory.
- Navigate to Pods > Development Pods >  react-native-brightcove-player.
- Right click on react-native-brightcove-player and select 'Show in Finder'.
- Navigate to ios directory and select the files CustomSlider.swift, GoogleCastManager.swift, PlayerViewController.swift and QualityPopover.swift.
- Drag and drop them to the react-native-brightcove-player section in Xcode. (Uncheck 'copy items if needed' option in the next pop up).
- Xcode will now ask for a confirmation for adding bridging header. Please select 'Create Bridging header' option.
- Add the brightcove import statement (#import "BrightcovePlayer.h") in the created bridging header file (react-native-brightcove-player-Bridging-Header.h).
- Right click on react-native-brightcove-player and select 'Show in Finder' again.
- Drag and drop Assets.xcassets to the project in xcode. Enable 'copy items if needed' option.

- Open AppDelegate.m and add the following import statements just after #import <React/RCTRootView.h>
```rb
#import <GoogleCast/GoogleCast.h>
#import <AVFoundation/AVFoundation.h>
```

- Navigate to the function ```- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions``` and add replace the following code:

```rb
 self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
  UIViewController *rootViewController = [UIViewController new];
  rootViewController.view = rootView;
  self.window.rootViewController = rootViewController;
  [self.window makeKeyAndVisible];
  return YES;
  ```
  with this:
  ```rb
  NSError * error = nil;
  BOOL success = [AVAudioSession.sharedInstance setCategory:AVAudioSessionCategoryPlayback mode:AVAudioSessionModeMoviePlayback routeSharingPolicy:AVAudioSessionRouteSharingPolicyLongForm options:0 error:nil];
  if(NO == success)
  {
    NSLog(@"Error setting category: %@", [error localizedDescription]);
  }


  // More Info @ https://developers.google.com/cast/docs/ios_sender/integrate#initialize_the_cast_context
     GCKDiscoveryCriteria *discoveryCriteria = [[GCKDiscoveryCriteria alloc] initWithApplicationID:kGCKDefaultMediaReceiverApplicationID];
     GCKCastOptions *options = [[GCKCastOptions alloc] initWithDiscoveryCriteria:discoveryCriteria];
     [GCKCastContext setSharedInstanceWithOptions:options];

     // More Info @ https://developers.google.com/cast/docs/ios_sender/integrate#add_expanded_controller
     [GCKCastContext sharedInstance].useDefaultExpandedMediaControls = YES;
  
  self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
  UIViewController *rootViewController = [UIViewController new];
  rootViewController.view = rootView;
  self.window.rootViewController = rootViewController;
  [self.window makeKeyAndVisible];
  return YES;
  ```
  
- Add all the configurations required for google cast as mentioned in the documentation at https://developers.google.com/cast/docs/ios_sender


- 
### Android

- In project/build.gradle file make changes 
```rb

  buildscript {
    ext {
        buildToolsVersion = "32.0.0"
        minSdkVersion = 21
        compileSdkVersion = 32
        targetSdkVersion = 32
        ndkVersion = "21.4.7075529"
        androidXCore = "1.6.0"

    }
    repositories {
        google()
        mavenCentral()
    }
    dependencies {
        classpath("com.android.tools.build:gradle:4.2.0")
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
 }
    
  allprojects {
    repositories {
       mavenLocal()
       maven {
            url 'https://repo.brightcove.com/releases'
                    allowInsecureProtocol = true
        }
        maven {
            // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
            url("$rootDir/../node_modules/react-native/android")
        }
        maven {
            // Android JSC is installed from npm
            url("$rootDir/../node_modules/jsc-android/dist")
        }
        mavenCentral {
            // We don't want to fetch react-native from Maven Central as there are
            // older versions over there.
            content {
                excludeGroup "com.facebook.react"
            }
        }
        google()
        jcenter()
        maven { url 'https://www.jitpack.io' }
    }
  }
```
- Then in AndroidManifest.xml file 

- Add Following Permissions

```rb
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.FOREGROUND_SERVICE" />
    <uses-feature
        android:name="android.hardware.wifi"
        android:required="true" >
    </uses-feature>
```
- Then add following lines above </application> tag

```rb

        <meta-data
            android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />

        <meta-data
            android:name="com.google.android.gms.cast.framework.OPTIONS_PROVIDER_CLASS_NAME"
            android:value="com.brightcove.cast.DefaultOptionsProvider" />

        <activity
            android:name="com.brightcove.cast.DefaultExpandedControllerActivity"
            android:label="@string/app_name"
            android:exported="true"
            android:launchMode="singleTask"
            android:theme="@style/Theme.BrightcoveCast"
            android:screenOrientation="portrait"
            android:parentActivityName=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN"/>
            </intent-filter>
        </activity>
        <meta-data
            android:name="com.brightcove.cast.DefaultOptionsProvider.EXPANDED_CONTROLLER_ACTIVITY_CLASS_NAME"
            android:value="com.brightcove.cast.DefaultExpandedControllerActivity" />
        <meta-data
            android:name="com.brightcove.cast.DefaultOptionsProvider.NOTIFICATION_TARGET_ACTIVITY_CLASS_NAME"
            android:value="com.brightcove.cast.DefaultExpandedControllerActivity" />
            
```
- Then add picture in piture support to the Activity
```rb
        android:supportsPictureInPicture="true"
        android:configChanges="screenSize|smallestScreenSize|screenLayout|orientation"
```
- Then add 
```rb
    <application
    ...
             android:largeHeap="true"
    ...
```
in <application> Tag.
## API

### BrightcovePlayer

```jsx
import { BrightcovePlayer } from 'react-native-brightcove-player';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <BrightcovePlayer
          style={styles.player}
          accountId="3636334163001"
          videoId="3666678807001"
          policyKey="BCpkADawqM1W-vUOMe6RSA3pA6Vw-VWUNn5rL0lzQabvrI63-VjS93gVUugDlmBpHIxP16X8TSe5LSKM415UHeMBmxl7pqcwVY_AZ4yKFwIpZPvXE34TpXEYYcmulxJQAOvHbv2dpfq-S_cm"
        />
      </View>
    );
  }
}
```

- Video player component of Brightcove.
- It may not work on Android simulator.
- For a more detailed example, please see [example](https://github.com/manse/react-native-brightcove-player/blob/master/example/).

| Prop                   | Type     | Description                                                                                                                                                                                                                       | Event Object                 |
| ---------------------- | -------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------- |
| accountId              | string   | Brightcove AccountId. Required                                                                                                                                                                                                    |                              |
| policyKey              | string   | Brightcove PolicyKey. Required                                                                                                                                                                                                    |                              |
| videoId                | string   | Brightcove VideoId to playback.                                                                                                                                                                                                   |                              |
| referenceId            | string   | Brightcove ReferenceId to playback.                                                                                                                                                                                               |                              |
| videoToken             | string   | Offline video token that generated by [BrightcovePlayerUtil](#BrightcovePlayerUtil). Video download session must be completed before setting prop and playback. If set `videoToken`, `videoId` and `referenceId` will be ignored. |                              |
| autoPlay               | boolean  | Set `true` to play automatically                                                                                                                                                                                                  |                              |
| play                   | boolean  | Control playback and pause                                                                                                                                                                                                        |                              |
| fullscreen             | boolean  | Control full screen state                                                                                                                                                                                                         |                              |
| volume                 | number   | Set audio volume (`0.0 ~ 1.0`)                                                                                                                                                                                                    |                              |
| bitRate                | number   | Set maximum buffering bitrate. Set `0` to automatic quality                                                                                                                                                                       |                              |
| playbackRate           | number   | Set playback speed scale. Default is `1`                                                                                                                                                                                          |                              |
| disableDefaultControl  | boolean  | Disable default player control. Set `true` when you implement own video controller.                                                                                                                                               |                              |
| onReady                | Function | Indicates the video can be played back                                                                                                                                                                                            |                              |
| onPlay                 | Function | Indicates the video playback starts                                                                                                                                                                                               |                              |
| onPause                | Function | Indicates the video is paused                                                                                                                                                                                                     |                              |
| onEnd                  | Function | Indicates the video is played to the end                                                                                                                                                                                          |                              |
| onProgress             | Function | Indicates the playback head of the video advances.                                                                                                                                                                                | `{ currentTime: number }`    |
| onChangeDuration       | Function | Indicates the video length is changed                                                                                                                                                                                             | `{ duration: number }`       |
| onUpdateBufferProgress | Function | Indicates the video loading buffer is updated                                                                                                                                                                                     | `{ bufferProgress: number }` |
| onEnterFullscreen      | Function | Indicates the player enters full screen                                                                                                                                                                                           |                              |
| onExitFullscreen       | Function | Indicates the player exit full screen                                                                                                                                                                                             |                              |

| Method                                | Description                       |
| ------------------------------------- | --------------------------------- |
| seekTo(timeInSeconds: number) => void | Change playhead to arbitrary time |

### BrightcovePlayerPoster

```jsx
import { BrightcovePlayerPoster } from 'react-native-brightcove-player';

export default class App extends Component {
  render() {
    return (
      <View style={styles.container}>
        <BrightcovePlayerPoster
          style={styles.player}
          accountId="3636334163001"
          videoId="3666678807001"
          policyKey="BCpkADawqM1W-vUOMe6RSA3pA6Vw-VWUNn5rL0lzQabvrI63-VjS93gVUugDlmBpHIxP16X8TSe5LSKM415UHeMBmxl7pqcwVY_AZ4yKFwIpZPvXE34TpXEYYcmulxJQAOvHbv2dpfq-S_cm"
          resizeMode="contain"
        />
      </View>
    );
  }
}
```

- Displays a poster specified by `videoId`, `referenceId` or `videoToken`.
- Prop is about the same as `BrightcovePlayer`.

| Prop        | Type   | Description                                                                                                                                                                                                                          |
| ----------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| accountId   | string | Required                                                                                                                                                                                                                             |
| policyKey   | string | Required                                                                                                                                                                                                                             |
| videoId     | string |                                                                                                                                                                                                                                      |
| referenceId | string |                                                                                                                                                                                                                                      |
| videoToken  | string |                                                                                                                                                                                                                                      |
| resizeMode  | string | Set the image resize method. Specifying `cover` or `contain` works the same as CSS keywords of `background-size`. Specifying `fit`, scales to fit the component size without considering aspect ratio of the image. Default value is `cover`. |

### BrightcovePlayerUtil

- Promise for all methods may be invoke `reject`. Be sure to catch the exception.

#### requestDownloadVideoWithVideoId, requestDownloadVideoWithReferenceId

```ts
BrightcovePlayerUtil.requestDownloadVideoWithVideoId(accountId: string, policyKey: string, videoId: string, bitRate?: number): Promise<string>
BrightcovePlayerUtil.requestDownloadVideoWithReferenceId(accountId: string, policyKey: string, referenceId: string, bitRate?: number): Promise<string>
```

- Starts downloading the specified video with `videoId` or `referenceId` for offline playback.
- Returns `VideoToken` in string wrapped with `Promise`. This value will be used for offline playback with `BrightcovePlayer`, acquisition for download status or deletion of offline videos.
  - Note that this promise resolves the download start, not the download complete.
  - In case that specified video has already been downloaded `Promise` will be rejected.
  - If the download is in progress, the download is retried on iOS and rejected on Android.
- Does not work properly on simulator.
- `bitrate` controls the quality of the downloading video in `bps`.
  - Downloads a rendition with the largest bitrate less than or equal to this value.
  - If no rendition can be found with a bitrate that is smaller than or equal to this bitrate, the lowest rendition will be downloaded.
  - If this value is `0` or not specified, the lowest rendition with video will be downloaded.

#### getOfflineVideoStatuses

```ts
BrightcovePlayerUtil.getOfflineVideoStatuses(accountId: string, policyKey: string): Promise<{
  accountId: string;
  videoId: string;
  videoToken: string;
  downloadProgress: number;
}[]>
```

- Lists downloaded offline videos and downloading progresses.
- `downloadProgress` equals `1` indicates that offline playback available.
  - Otherwise the download is in progress and the value shows the progress ratio in `0.0 ~ 1.0`.
- Note that iOS returns offline videos for all accounts but Android only returns for the specified account. In other words iOS does not consider `accountId` and `policyKey`.

#### deleteOfflineVideo

```ts
BrightcovePlayerUtil.deleteOfflineVideo(accountId: string, policyKey: string, videoToken: string): Promise<void>
```

- Deletes offline videos or abort the ongoing download session.

#### addOfflineNotificationListener

```ts
BrightcovePlayerUtil.addOfflineNotificationListener(callback: (statuses: {
  accountId: string;
  videoId: string;
  videoToken: string;
  downloadProgress: number;
}[]) => void): Function
```

- Register a callback to notify storage changes such as video download progress or deletion of offline video.
- Make sure call disposer function when callback is no longer needed.

```ts
class Example extends Component {
  componentDidMount() {
    this.disposer = BrightcovePlayerUtil.addOfflineNotificationListener(console.log);
  }

  componentWillUnmount() {
    this.disposer();
  }

  render() {
    ...
  }
}
```

#### getPlaylistWithPlaylistId, getPlaylistWithReferenceId

```ts
BrightcovePlayerUtil.getPlaylistWithPlaylistId(accountId: string, policyKey: string, playlistId: string): Promise<{
  accountId: String;
  videoId: String;
  referenceId: String;
  name: String;
  description: String;
  duration: number;
}[]>;
BrightcovePlayerUtil.getPlaylistWithReferenceId(accountId: string, policyKey: string, referenceId: string): Promise<{
  accountId: String;
  videoId: String;
  referenceId: String;
  name: String;
  description: String;
  duration: number;
}[]>;
```

- Retrieves a playlist specified by `playlistId` or `referenceId`.

## License

MIT
