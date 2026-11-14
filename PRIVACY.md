# Privacy Policy — Middle-Trimmer

**Last updated: 14 November 2026**

Middle-Trimmer ("Middle Trimmer" in the Microsoft Store) is a Windows desktop application published by Dr. Xu's Studio ("we", "us"). This policy covers Middle-Trimmer 1.6.3 and later, distributed through the Microsoft Store and as a direct download.

## Summary

Middle-Trimmer processes your videos and audio on your own PC. It has no account, no sign-in, no analytics, no telemetry, no crash reporting, and no advertising. It does not collect, transmit, sell, or share personal information. Your files are never uploaded anywhere.

## What is processed, and where

Everything Middle-Trimmer does happens locally, on the machine it is running on:

- **Your video and audio files** are opened, decoded, trimmed and re-encoded by an FFmpeg build that ships inside the application. The files are read from and written to the folders you choose; they are not sent over the network.
- **Timestamps, output name, volume and mode settings** are passed to that local FFmpeg process as command-line arguments.
- **The optional AI noise reduction** runs a local speech-enhancement model in a local Python environment (see the next two sections). The audio is not uploaded; only the model weights are downloaded, and only if you opt in.

The developer receives nothing: no file names, no paths, no durations, no usage counts, no error reports.

## Optional AI noise reduction (opt-in, off by default)

The `AI MossFormer2` mode is optional. Nothing about it happens unless you enable it.

**On the Store build**, the one-click "Set up AI noise reduction" installer is unavailable, because the Microsoft Store does not permit an application to download and install a run-time programming environment. `AI MossFormer2` stays visible, and works only if you already have a compatible environment that the application can find (a previous direct-download install, or a Python you point it at). If no environment is configured, the mode explains this and stays disabled.

**On the direct-download build**, if you choose "Set up AI noise reduction", the application downloads and installs a private Python environment into `%LOCALAPPDATA%\MiddleTrimmer\ai-env`:

- a pinned Python 3.11 runtime archive from the `astral-sh/python-build-standalone` GitHub releases, verified against a pinned SHA-256 before it is unpacked;
- the packages listed in `requirements-ai.txt` (PyTorch CPU wheels, ClearVoice and its dependencies) from PyPI and from `download.pytorch.org`, with HTTPS.

Those downloads are ordinary file downloads. The servers involved see your IP address, as any download server does; they receive no information about your files or your usage. We do not control those servers and they handle that data under their own policies.

**The model itself** is downloaded the first time AI noise reduction actually runs, and only then: a single checkpoint from the public `alibabasglab/MossFormer2_SE_48K` repository on Hugging Face, into `<ai-env>\checkpoints\MossFormer2_SE_48K\`, and verified against a pinned SHA-256. Hugging Face sees the request and your IP address. The audio you are processing is not part of that request.

## Network requests

- **Store build, default use:** none. Trimming a video with the bundled FFmpeg makes no network request at all.
- **Store build, with an AI environment you configured yourself:** one download of the model checkpoint from Hugging Face on first use of the AI mode, as described above.
- **Direct-download build, if you run the AI setup:** the Python/PyPI/PyTorch downloads described above, plus the Hugging Face checkpoint download.

There are no other outbound connections: no update check, no license check, no analytics endpoint, no advertising, no third-party SDK.

## Local storage

- `%APPDATA%\MiddleTrimmer\config.json` — the small settings file written on first use. It records your preferences (for example the last-used folders and options) and which AI environment, if any, is configured. It contains no personal information beyond the folder paths you chose, and deleting it resets the application to defaults.
- `%LOCALAPPDATA%\MiddleTrimmer\ai-env\` — the optional AI environment described above, including the downloaded model checkpoint (a few hundred MB). This folder exists only if you opted into AI noise reduction.

The application also reads the media files you open, and writes trimmed output and temporary working files to the folders involved in that operation. Temporary files are removed when the operation finishes.

The application does not write to the Windows registry.

## Removing the optional AI environment

You are never locked in. Removing it means deleting its folder, which the application's own help text documents:

1. Delete the folder `%LOCALAPPDATA%\MiddleTrimmer\ai-env`; and optionally
2. Delete `%APPDATA%\MiddleTrimmer\config.json`, which also clears the recorded preference.

After removal, `AI MossFormer2` is simply unavailable again; trimming, manual mode, and every other feature keep working, because they use the bundled FFmpeg and nothing else.

## Third-party software

The Microsoft Store package contains an unmodified, LGPL-licensed FFmpeg build (version `n8.1.3-35-gf1acb0a266`, from BtbN/FFmpeg-Builds) that runs as a separate local process. It is used only to read and write your media files and makes no network requests. Its licence and full attribution travel with the package as `third-party\FFmpeg-LICENSE.txt` and `third-party\FFmpeg-NOTICE.md`.

## Children

Middle-Trimmer is a video utility and is not directed at children. It collects no information from anyone, including children.

## Changes

If this policy changes, the revised version will be posted at this address with a new "last updated" date.

## Contact

Questions: use the support instructions on the download page.
