# Movy for TV

Sideloadable Android TV / Fire TV shell for [movy.sx](https://movy.sx) with real D-pad navigation.
The site ships no TV app and its cards are plain links, so a remote only scrolls the page.

This app wraps the site in a fullscreen WebView and injects a spatial-navigation layer
(`assets/tvnav.js`): arrow keys move a white focus ring to the nearest tile in that direction,
rows scroll sideways, OK opens, Back closes a search field before it goes back, and the
Play/Pause plus ±10 s seek media keys drive the video element.

| Key | Action |
| --- | --- |
| D-pad | Move selection |
| OK / Enter / Select | Open focused item, play, submit |
| Back | Leave a text field, then page back; on the home page it exits |
| Play / Pause | Toggle playback |
| Fast-forward / Rewind | Seek ±10 s |

## Install

Download `MovyTV.apk` from the latest release, then either

- on the TV: install the **Downloader** app, allow unknown sources, enter the APK URL, install, launch **Movy**;
- or over ADB: `adb connect <tv-ip>:5555 && adb install MovyTV.apk`.

minSdk 21 (Android 5.0+). Permissions: internet only. No ads, trackers or accounts.

## Build

```bash
${ANDROID_HOME}/build-tools/34.0.0/aapt2 compile --dir res -o build/res.zip
${ANDROID_HOME}/build-tools/34.0.0/aapt2 link -o build/base.apk -I ${ANDROID_HOME}/platforms/android-34/android.jar \
  --manifest AndroidManifest.xml -A assets --min-sdk-version 21 --target-sdk-version 34 build/res.zip
javac -source 8 -target 8 -bootclasspath ${ANDROID_HOME}/platforms/android-34/android.jar -d build/classes src/sx/movy/tv/MainActivity.java
${ANDROID_HOME}/build-tools/34.0.0/d8 --lib ${ANDROID_HOME}/platforms/android-34/android.jar --min-api 21 --output build/dex build/classes/sx/movy/tv/*.class
# add build/dex/classes.dex to the APK, zipalign, apksigner sign
```

`svc://` nothing special: the URL can be overridden for testing with
`am start -n sx.movy.tv/.MainActivity -e url https://www.movy.sx/browse/movie`.

Not applicable to LG webOS or Samsung Tizen sets, which cannot run APKs; their built-in
browsers pair with a pointer remote and already work with the site's hover UI.
