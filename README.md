# gym_app_releases

Release config and install assets for the gym app.

`config/release.json` is what the app fetches on splash. The current Android APK is the `version-1.3.0+6` release on this repo.

## What the app reads

Public raw URL:

`https://raw.githubusercontent.com/Zaaraa96/gym_app_releases/main/config/release.json`

Point the app at that URL (`RELEASE_CONFIG_URL` in `lib/app/release_gate_bootstrap.dart`, or `--dart-define=RELEASE_CONFIG_URL=…`).

| Field | Meaning |
| --- | --- |
| `minBuild` | Builds below this are forced to update |
| `latestBuild` | Newest published build. Higher than the installed build is an optional update |
| `androidUrl` | Direct download for the Android APK |
| `iosUrl` | App Store or TestFlight link. Empty until iOS ships |
| `optionalMessage` | Copy on the optional update sheet |
| `forceMessage` | Copy on the blocking update sheet |

## Publishing an Android APK here

`gym_app_layout` `.github/workflows/cd.yml` publishes the APK here with the `GYM_APP_RELEASE_TOKEN` secret. That token needs **Contents: Read and write** on `Zaaraa96/gym_app_releases`. The layout repo’s `GITHUB_TOKEN` cannot write to this repo.

After that release exists, commit an updated `config/release.json` on `main`:

- set `latestBuild` to the pubspec build number
- set `androidUrl` to  
  `https://github.com/Zaaraa96/gym_app_releases/releases/download/<tag>/gym_app-<version>.apk`  
  (`+` in the tag and filename must be `%2B`)
- raise `minBuild` only when older builds must stop working

Do not commit the APK into git. GitHub Release assets on this repo are the download.
