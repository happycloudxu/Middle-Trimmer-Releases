# Privacy Policy — Middle-Trimmer

**Last updated: 15 November 2026**

Middle-Trimmer ("Middle Trimmer - Cut Video Middle" in the Microsoft Store) is a Windows desktop application published by Dr. Xu's Studio ("we", "us"). This policy covers Middle-Trimmer 1.6.3 and later, distributed through the Microsoft Store and as a direct download.

## Summary

Middle-Trimmer processes your videos and audio on your own PC. It has no account, no sign-in, no analytics, no telemetry, no crash reporting, and no advertising. It does not collect, transmit, sell, or share personal information. Your files are never uploaded anywhere.

## What is processed, and where

Everything Middle-Trimmer does happens locally, on the machine it is running on:

- **Your video and audio files** are opened, decoded, trimmed and re-encoded by an FFmpeg build that ships inside the application. The files are read from and written to the folders you choose; they are not sent over the network.
- **Timestamps, output name, volume and mode settings** are passed to that local FFmpeg process as command-line arguments.
- **The optional AI noise reduction** runs a local speech-enhancement model in a local Python environment (see the sections below). The audio is not uploaded; only the model weights are downloaded, and only if you opt in.
- **In-window playback** draws the clip you dropped through a VLC runtime on the same PC (see "Optional video preview" below). The video is not uploaded and nothing about the playback leaves the machine.

The developer receives nothing: no file names, no paths, no durations, no usage counts, no error reports.

## Optional AI noise reduction (opt-in, off by default)

The `AI MossFormer2` mode is optional. Nothing about it happens unless you enable it.

**On the Store build**, the one-click "Set up AI noise reduction" installer is unavailable, because the Microsoft Store does not permit an application to download and install a run-time programming environment. `AI MossFormer2` stays visible, and works only if you already have a compatible environment that the application can find (a previous direct-download install, or a Python you point it at). If no environment is configured, the mode explains this and stays disabled.

**On the direct-download build**, if you choose "Set up AI noise reduction", the application downloads and installs a private Python environment into `%LOCALAPPDATA%\MiddleTrimmer\ai-env`:

- a pinned Python 3.11 runtime archive from the `astral-sh/python-build-standalone` GitHub releases, verified against a pinned SHA-256 before it is unpacked;
- the packages listed in `requirements-ai.txt` (PyTorch CPU wheels, ClearVoice and its dependencies) from PyPI and from `download.pytorch.org`, with HTTPS.

Those downloads are ordinary file downloads. The servers involved see your IP address, as any download server does; they receive no information about your files or your usage. We do not control those servers and they handle that data under their own policies.

**The model itself** is downloaded the first time AI noise reduction actually runs, and only then: a single checkpoint from the public `alibabasglab/MossFormer2_SE_48K` repository on Hugging Face, into `<ai-env>\checkpoints\MossFormer2_SE_48K\`, and verified against a pinned SHA-256. Hugging Face sees the request and your IP address. The audio you are processing is not part of that request.

## Optional video preview (VLC)

Playing a clip inside the window needs a VLC runtime. Trimming never does: every trim, every audio job and every other feature uses the bundled FFmpeg and works on a PC with no VLC on it at all.

**If VLC is already installed**, Middle-Trimmer uses that copy in place. It finds it through `TRIM_MIDDLE_VLC_DIR` if you set that variable, otherwise through the folder a previous setup recorded, the `SOFTWARE\VideoLAN\VLC` registry key, or the usual Program Files folders. Nothing is copied, installed, uploaded or reported.

**On the Store build**, the one-click "Set up video preview" installer is unavailable, because the Microsoft Store does not permit an application to download and run its own runtime. Preview still works if you install VLC for Windows from videolan.org yourself, or point `TRIM_MIDDLE_VLC_DIR` at an existing copy. If neither is there, the app says so and every other feature keeps working.

**On the direct-download build**, if you choose "Set up video preview", the application downloads the official portable VLC archive that the app is pinned to (version 3.0.24, about 79 MB) from `download.videolan.org`:

- the archive is verified against a pinned SHA-256 before anything is unpacked;
- a corrupt, truncated or unexpected download is discarded, and nothing is installed;
- the runtime is unpacked into a private folder, `%LOCALAPPDATA%\MiddleTrimmer\vlc`;
- the folder is only moved into place after the runtime loads successfully, so cancelling or failing leaves no half-installed copy behind.

That is an ordinary file download. The servers involved see your IP address, as any download server does; they receive no information about your files or your usage. We do not control those servers and they handle that data under their own policies. Nothing is installed system-wide: no installer runs, no registry key is written and no administrator rights are needed. VLC belongs to the VideoLAN project and is licensed under the GNU GPL; it is not part of the application in any edition and is fetched only at your request.

## Network requests

- **Store build, default use:** none. Trimming a video with the bundled FFmpeg makes no network request at all.
- **Store build, with an AI environment you configured yourself:** one download of the model checkpoint from Hugging Face on first use of the AI mode, as described above.
- **Direct-download build, if you run the AI setup:** the Python/PyPI/PyTorch downloads described above, plus the Hugging Face checkpoint download.
- **Direct-download build, if you run the video-preview setup:** one download of the pinned VLC archive from `download.videolan.org`, as described above. Playing a clip afterwards makes no network request.

There are no other outbound connections: no update check, no license check, no analytics endpoint, no advertising, no third-party SDK.

## Local storage

- `%APPDATA%\MiddleTrimmer\config.json` — the small settings file written on first use. It records your preferences (for example the last-used folders and options) and which optional runtimes, if any, are configured: the `ai_python` key holds the AI environment's interpreter and `vlc_dir` holds the video-preview runtime folder. It contains no personal information beyond the folder paths you chose, and deleting it resets the application to defaults.
- `%LOCALAPPDATA%\MiddleTrimmer\ai-env\` — the optional AI environment described above, including the downloaded model checkpoint (a few hundred MB). This folder exists only if you opted into AI noise reduction.
- `%LOCALAPPDATA%\MiddleTrimmer\vlc\` — the optional video-preview runtime described above, which takes noticeably more space on disk than the ~79 MB download. This folder exists only if you ran the video-preview setup.

The application also reads the media files you open, and writes trimmed output and temporary working files to the folders involved in that operation. Temporary files are removed when the operation finishes.

The application does not write to the Windows registry.

## Removing the optional runtimes

You are never locked in. Each optional runtime is a folder you can delete, which the application's own help text documents:

- **AI noise reduction:** delete `%LOCALAPPDATA%\MiddleTrimmer\ai-env`. Afterwards `AI MossFormer2` is simply unavailable again.
- **Video preview:** delete `%LOCALAPPDATA%\MiddleTrimmer\vlc`. Afterwards preview uses a VLC you installed yourself if one is present, and otherwise offers to set one up again.

Optionally delete `%APPDATA%\MiddleTrimmer\config.json` as well, which clears both recorded paths and resets the application to defaults.

Nothing else depends on either folder: trimming, manual mode and every other feature keep working, because they use the bundled FFmpeg and nothing else.

## Third-party software

The Microsoft Store package contains an unmodified, LGPL-licensed FFmpeg build (version `n8.1.3-35-gf1acb0a266`, from BtbN/FFmpeg-Builds) that runs as a separate local process. It is used only to read and write your media files and makes no network requests. Its licence and full attribution travel with the package as `third-party\FFmpeg-LICENSE.txt` and `third-party\FFmpeg-NOTICE.md`.

VLC is separate again and ships with no edition of Middle-Trimmer. When you install VLC yourself, or the direct-download build fetches the official portable archive at your request, it is the VideoLAN build, licensed under the GNU General Public License, and it runs as a separate local process to draw the preview. The VideoLAN project publishes that build and its source code.

## Children

Middle-Trimmer is a video utility and is not directed at children. It collects no information from anyone, including children.

## Changes

If this policy changes, the revised version will be posted at this address with a new "last updated" date.

## Contact

Questions: use the support instructions on the download page.
