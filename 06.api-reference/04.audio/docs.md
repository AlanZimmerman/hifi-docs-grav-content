---
title: 'Audio'
taxonomy:
    category:
        - docs
---


| Methods                                  |
| ---------------------------------------- |
| [Audio.playSound(SharedSoundPointer)](#method-1) |
| Audio.playSound(SharedSoundPointer,AudioInjectorOptions) |
| Audio.setStereoInput(bool)               |
| [Audio.stopInjector(soundPlaying)](#method-3) |
| [Audio.isInjectorPlaying(soundPlaying)](#method-2) |


| Events                          |
| ------------------------------- |
| Audio.disconnected()            |
| Audio.environmentMuted()        |
| Audio.inputReceived(QByteArray) |
| Audio.mutedByMixer()            |
| Audio.noiseGateClosed()         |
| Audio.noiseGateOpened()         |
| Audio.receivedFirstPacket()     |



## Audio.playSound() <a id="method-1"></a>

`Audio.playSound()` triggers playback of an audio file by sending it to the audio mixer. To use this API to play (aka "inject") a sound, the sound file must have been previously loaded using [SoundCache.getSound()](https://wiki.highfidelity.com/wiki/SoundCache.getSound()).

If the client is not connected to an audio mixer, no sound will be played unless the localOnly option is invoked (see below.)

### Function

`Audio.playSound (Sound*, AudioInjectorOptions)`

### Arguments

**Sound\*** The sound object returned from [SoundCache.getSound()](https://wiki.highfidelity.com/wiki/SoundCache.getSound()).

Below are options you can pass to `Audio.playSound()`:

| Option         | Description                              | Values                                   | Default                    |
| -------------- | ---------------------------------------- | ---------------------------------------- | -------------------------- |
| Volume         | Playback volume of sound                 | from 0.0 to 1.0                          | 1.0                        |
| Position       | Playback position of sound               | Valid positional coordinates within the domain | { x: 0, y: 0, z: 0 }       |
| Orientation    | Playback orientation of sound            | As above                                 | { x: 0, y: 0, z: 0, w: 1 } |
| Loop           | Repeats sound automatically              | Boolean                                  | False                      |
| Stereo         | File is in stereo format                 | Boolean                                  | False                      |
| localOnly      | injects audio locally rather than via the audio mixer | Boolean                                  | False                      |
| secondOffset   | starting playback point within the soundclip, in seconds | Any number >= 0                          | 0                          |
| ignorePenumbra | don't subject this sound to the positional lowpass penumbra filter | Boolean                                  | False                      |

'Stereo' can be overridden by a WAV header or by including ".stereo" in a RAW filename (e.g., Thunderclap.stereo.raw)

Note that 'Position', 'Orientation' and 'IgnorePenumbra' have no effect when 'localOnly' is true. Local sounds are played without any spatialization.

### Returns

**object: AudioInjector**

### Examples

To play a sound loaded into the SoundCache as "GunshotSound":

```
var injector = Audio.playSound(GunshotSound);

```

This will inject the sound by sending it to the audio mixer. The object returned from a call to Audio.playSound is an 'AudioInjector'. While it is running you can change the options above via the property '.options'. To modify the injector so that the sound plays from a position in the domain with coordinates of x: 10, y: 20, z: 30:h

```
injector.options = { position: { x: 10, y: 20, z: 30 } }

```

You can also pass options to Audio.playSound initially with the same name-value pairs.

```
var options = { loop: true, position: { x: 10, y: 20, z: 30 } }
var injector = Audio.playSound(options);

```

There are a number of methods you can call on the 'AudioInjector' object to control the playback and get information about its current state.

```java
injector.restart(); // stops playback and restarts from beginning - useful for repeated sounds 
injector.stop() // stops playback immediately
injector.isPlaying(); // true if still playing
injector.getLoudness(); // returns the loudness in last frame as a value between 0 and 1.0
injector.finished.connect(function();    // your injector is done playing
```

##  Audio.isInjectorPlaying() <a id="method-2"></a>

Description: `isInjectorPlaying` returns a boolean value describing the play status of a given AudioInjector.

### Example: Using isInjectorPlaying()

Take a look at this snippet of `playSoundLoop.js`:

```java
//starting the sound
soundPlaying = Audio.playSound(sound, options);
print("Started sound looping.");
Script.update.disconnect(maybePlaySound);
	}
}

function scriptEnding() {

//if the injector is playing a sound, Audio.isInjectorPlaying(soundPlaying) returns `true`
if (Audio.isInjectorPlaying(soundPlaying)) {
Audio.stopInjector(soundPlaying);
Entities.deleteEntity(ball);
print("Stopped sound.");
}

```

## Audio.stopInjector() <a id="method-3"></a>

Description: `stopInjector` stops the playback of a given AudioInjector.

### Example: Using stopInjector()

Take a look at this snippet of `playSoundLoop.js`:

```java
 function maybePlaySound(deltaTime) {
 
 ...
 //starting the sound
 soundPlaying = Audio.playSound(sound, options);
 print("Started sound looping.");
 Script.update.disconnect(maybePlaySound);
 }
 }
 
 function scriptEnding() {
 if (Audio.isInjectorPlaying(soundPlaying)) {
 
 //stopping the sound
 Audio.stopInjector(soundPlaying);
 Entities.deleteEntity(ball);
 print("Stopped sound.");
 }
 
 ...
```