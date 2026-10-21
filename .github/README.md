# PdfCraft for Android (early preview)

An unofficial Android build of [PdfCraft](https://github.com/storytold/pdfcraft), the open-source PDF workbench
written in Rust by the ArtCraft team, a clean-room take on the Acrobat workflow. This build runs it as a native Android
app (egui + wgpu), not as a web page.

<img width="1440" height="2304" alt="Screenshot_20261021_224437" src="https://github.com/user-attachments/assets/05dfe6f9-f5e3-4234-9635-d98dc74e2b3c" />


> **Early preview.** This is a first working port, not a finished product. See [Limitations](#limitations).

This repository does not contain PdfCraft's source. A GitHub Actions workflow downloads the upstream source,
adds a small Android layer, builds the APK and publishes it under [Releases](../../releases).

## Download

Get the latest `.apk` from the [Releases](../../releases) page, open it and allow the install when Android asks.
You need:

- an ARM64 phone or tablet (most devices from the last several years)
- Android 9 or newer
- a GPU with Vulkan

If an update won't install over an older build, uninstall the old one first.

## Using it

No storage permission is needed. Every Open, Save As and Export dialog is Android's own file picker, so you can choose
documents anywhere: internal storage, an SD card, or cloud apps such as Google Drive.

PdfCraft works on a private copy of the document you pick and writes every save back to the original location within a
couple of seconds. If the source is read-only (for example an email attachment), use **Save As** to choose a place you
can write to.

You can also open a PDF from another app: choose **PdfCraft** in the "Open with" list.

Your settings and Data Recovery copies are kept in the app's private folder. Links open in your browser, and the
app's "releases" link opens this repository's Releases page.

## Limitations

- No system clipboard yet: copying images to or from other apps does nothing.
- No printing, and no update check (use the Releases page).
- Commands that ask for a **folder** (batch actions, export to a folder) only work after you turn on
  Settings → Apps → PdfCraft → Permissions → Files → **Allow access to manage all files**; without it the folder
  dialog is cancelled.
- A hardware keyboard and mouse or pen are recommended; the interface is the desktop layout, not redesigned for small
  touch screens.
- Large documents, OCR and heavy rendering can be slow on low-end devices.

## How builds are made

- The workflow in `.github/workflows/pdfcraft-android-release.yml` runs every day and builds when upstream has a new
  commit, or when the Android layer file changes.
- To build right away: **Actions → Build and release PdfCraft Android APK → Run workflow**. You can enter an
  upstream branch or tag, or tick *force* to rebuild a version that already has a release.
- To stay on one upstream version, add a repository variable named `UPSTREAM_REF` (Settings → Secrets and variables →
  Actions → Variables) set to a tag or commit.

The Android layer lives in `android-layer/pdfcraft-android-layer.tar.gz`. It adds an Android app crate
(`apps/pdfcraft-android`), a Java launcher activity, stand-ins for the `rfd` file dialog and `arboard` clipboard crates
(which have no Android backend), Android input files for PdfCraft's vendored `winit`, and an Android manifest. It
changes PdfCraft's own code only to add the releases link and the Android font folder. If upstream changes in a way
that breaks the layer, the build stops and the log says why.

## Signing

Builds are signed with a key the workflow creates and caches. For a permanent key, add the secrets
`ANDROID_KEYSTORE_B64` (a PKCS12 keystore, base64-encoded, with alias `pdfcraft`) and `ANDROID_KEYSTORE_PASSWORD`.
APKs signed with different keys can't update each other.

## Credits and licence

- PdfCraft is by the [ArtCraft](https://getartcraft.com/) team, under MIT or Apache-2.0.
- The Android approach follows the community PhotoCraft port,
  [SnowOwlLeader/photocraft-for-android](https://github.com/SnowOwlLeader/photocraft-for-android).
- The Android input files in `vendor/winit` come from that port (winit 0.30.13, Apache-2.0).

This project is not affiliated with or supported by the PdfCraft or ArtCraft team.
