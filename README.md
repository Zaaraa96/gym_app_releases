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
| `androidUrl` | Download for builds that do not choose an ABI. The arm64-v8a CueLift APK |
| `androidUrlByAbi` | Split APK URLs by ABI: `armeabi-v7a`, `arm64-v8a`, `x86_64`. The app uses the device ABI and falls back to `androidUrl` |
| `iosUrl` | App Store or TestFlight link. Empty until iOS ships |
| `optionalMessage` | Copy on the optional update sheet |
| `forceMessage` | Copy on the blocking update sheet |
| `optionalMessageByLocale` | Optional body by language (`en` / `fa`) |
| `forceMessageByLocale` | Force body by language (`en` / `fa`) |

## Publishing an Android APK here

`gym_app_layout` `.github/workflows/cd.yml` publishes the APK here with the `GYM_APP_RELEASE_TOKEN` secret. That token needs **Contents: Read and write** on `Zaaraa96/gym_app_releases`. The layout repo’s `GITHUB_TOKEN` cannot write to this repo.

After that release exists, commit an updated `config/release.json` on `main`:

- set `latestBuild` to the pubspec build number
- set `androidUrl` to the arm64-v8a asset  
  `https://github.com/Zaaraa96/gym_app_releases/releases/download/<tag>/cuelift-<version>-arm64-v8a.apk`  
  (`+` in the tag and filename must be `%2B`)
- set `androidUrlByAbi` to that same pattern for `armeabi-v7a`, `arm64-v8a`, and `x86_64`
- raise `minBuild` only when older builds must stop working. Leave it unchanged when the only difference is the CueLift per-ABI filenames

Do not commit the APK into git. GitHub Release assets on this repo are the download.
