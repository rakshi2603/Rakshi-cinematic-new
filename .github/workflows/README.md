# Rakshi Cinematic — local scene analysis and multiple inputs

## Install this upgrade

1. Replace all of .github/workflows/build.yml in rakshi2603/Rakshi-cinematic-new with build-v2-ai.yml from this package. Keep the repository filename build.yml.
2. Commit to main. The workflow unpacks the existing base project, adds the bundled ML Kit dependency, writes MainActivity.kt, and builds the debug APK.
3. Download the APK artifact from the successful run and install it.
4. If Android rejects the update because GitHub generated a different debug signing key, save any existing reels to Gallery before uninstalling the old app. Uninstalling removes its private exported files. A stable signing key is not included in this upgrade.

## What changed

The previous working app selected evenly spaced clips from one video and wrote an MP4 to app-private storage. This upgrade accepts multiple videos with GetMultipleContents, analyses frames from every input, ranks candidate scenes across the complete selection, and concatenates chosen clips in the order returned by the picker, then timestamp order within each source.

Choose CAR or BIKE and a 15-, 30-, or 60-second target before selecting videos. Selecting videos replaces the current selection. Changing the preset or duration requires selecting the videos again. Each timeline row names its source video number.

The model dependency is com.google.mlkit:image-labeling:17.0.9, the bundled image labelling model. Google documents this bundled route at https://developers.google.com/ml-kit/vision/image-labeling/android . The APK build downloads the dependency; frame inference and video rendering run on the phone. No footage-upload code or cloud API is added.

Candidates are non-overlapping four-second windows, sampled near their centre. The model produces general scene labels. Ranking combines relevant car/bike/scenery labels, usable exposure, image detail, and coarse differences between sampled frames. This is an initial ML-assisted highlight ranker, not a trained GoPro highlight detector. It does not measure vehicle speed, recognise driving manoeuvres, stabilise footage, or perform beat matching.

The analysis budget is approximately 1,200 frames across the selected inputs (at least one per input). Longer recordings are sampled more sparsely. Dark or undecodable frames may reduce the exported duration below the requested target. If source duration is below the target and frames are readable, most or all source footage may still be selected. The app reports the actual selected duration.

Save to Gallery copies the completed private MP4 to Movies/RakshiCinematic using MediaStore. Play and Share use the Gallery content URI. No broad storage permission is needed on the Samsung S25 Ultra. Gallery saving requires Android 10 or newer.

## Verification status

The original local exporter built successfully and reported export completion on the user's S25 Ultra. This upgrade has passed YAML parsing, exact embedded-source round-trip verification, and shell-script syntax checks. The dependency-writing script was exercised with temporary Groovy and Kotlin Gradle files and checked for idempotence.

This upgrade has NOT been compiled here: an Android SDK, Gradle dependency cache, and Kotlin compiler are unavailable. The next GitHub Actions run must establish compilation. Model execution, multi-input export, mixed-resolution/codec footage, audio continuity, Gallery saving, and sharing require phone testing.

## Phone acceptance checks

- Select two or more short videos, choose a 15-second target, and verify timeline rows identify source videos.
- With Wi-Fi and mobile data off, run analysis and export from locally stored inputs.
- Select inputs totalling less than the target and confirm actual output duration is reported rather than padded.
- Save to Gallery, play the MP4, and verify picture, audio, and duration; share it to an app of your choice.
- Repeat with your actual GoPro codec/resolution combinations. The renderer may re-encode mixed inputs; HDR and codec compatibility are not proven.
- Keep the app open during analysis/export. Background processing and restoration after rotation or process termination are not implemented.

Music, colour grading, transitions, preview/reordering, long-running foreground jobs, and trained aesthetic/GoPro highlight models remain future work.

## Provenance

Requested by Rakshith after the working single-input exporter was tested on 11 October 2026 IST. Prepared by ChatGPT using the current repository workflow as the export foundation. Existing GitHub integration write access returned 403, so this is a reviewable replacement package for manual upload. No repository changes were pushed in this upgrade.
