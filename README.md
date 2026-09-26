# gym_app_releases

Release config and install assets for the gym app.

`config/release.json` is what the app fetches on splash. The current Android APK still lives on the `gym_app_layout` GitHub Release (`version-1.4.0+6`). New APKs belong on this repo.

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

The layout repo’s `GITHUB_TOKEN` cannot upload to this repo. Add a fine-grained personal access token with **Contents: Read and write** on `Zaaraa96/gym_app_releases`, and store it on `gym_app_layout` as `RELEASES_REPO_TOKEN`.

In `.github/workflows/cd.yml`, publish the built APK to this repo instead of `gym_app_layout`:

```yaml
- name: Create GitHub Release
  if: github.event_name == 'push' && startsWith(github.ref, 'refs/tags/')
  env:
    GH_TOKEN: ${{ secrets.RELEASES_REPO_TOKEN }}
  run: |
    gh release create "${{ github.ref_name }}" \
      --repo Zaaraa96/gym_app_releases \
      --title "${{ github.ref_name }}" \
      --notes "$notes" \
      "dist/gym_app-${APP_VERSION}.apk"
```

After that release exists, commit an updated `config/release.json` on `main`:

- set `latestBuild` to the pubspec build number
- set `androidUrl` to  
  `https://github.com/Zaaraa96/gym_app_releases/releases/download/<tag>/gym_app-<version>.apk`  
  (`+` in the tag and filename must be `%2B`)
- raise `minBuild` only when older builds must stop working

Do not commit the APK into git. GitHub Release assets on this repo are the download.
