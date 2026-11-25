# Privacy Policy — Middle-Trimmer

**Last updated: 25 November 2026**

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

**Both editions behave the same way.** If you choose "Set up AI noise reduction", the application downloads and installs a private Python environment into `%USERPROFILE%\.middle-trimmer\ai-env`. Both the direct-download build and the Microsoft Store (MSIX) copy use that one folder: it is deliberately outside the package's own storage, because Windows deletes a package's local folder when the app is uninstalled, and keeping the environment in your profile means an update or a reinstall does not have to download it again. The other side of that: uninstalling Middle Trimmer does **not** delete `%USERPROFILE%\.middle-trimmer`, so delete that folder yourself if you want the space back. `TRIM_MIDDLE_AI_ROOT` overrides the location in either edition. Nothing is installed until you ask for it: `AI MossFormer2` stays disabled until an environment exists, and if you already have a compatible one (made by hand earlier, or a Python you point it at) the installer is not needed at all. An environment that still works is used exactly as it is, wherever an earlier version put it. If only the recorded path survives and the interpreter behind it is gone — a package folder Windows deleted, say — the setting is re-pointed at `%USERPROFILE%\.middle-trimmer\ai-env` on first launch, and whatever is left of the old folder stays on disk until you delete it.

Each setup attempt also writes `ai-setup.log` into the folder that holds the environment (see "Local storage" below): the commands it ran, their exit codes and the end of their output. It exists so that a failure can be explained without guessing, contains no personal information, and is overwritten by the next attempt.

What that installer downloads, when you choose it:

- a pinned Python 3.11 runtime archive from the `astral-sh/python-build-standalone` GitHub releases, verified against a pinned SHA-256 before it is unpacked;
- the packages listed in `requirements-ai.txt` (PyTorch CPU wheels, ClearVoice and its dependencies) from PyPI and from `download.pytorch.org`, with HTTPS.

Those downloads are ordinary file downloads. The servers involved see your IP address, as any download server does; they receive no information about your files or your usage. We do not control those servers and they handle that data under their own policies.

**The model itself** is downloaded the first time AI noise reduction actually runs, and only then: a single checkpoint from the public `alibabasglab/MossFormer2_SE_48K` repository on Hugging Face, into `%USERPROFILE%\.middle-trimmer\checkpoints\MossFormer2_SE_48K\`, and verified against a pinned SHA-256. Hugging Face sees the request and your IP address. The audio you are processing is not part of that request.

## Optional video preview (VLC)

Playing a clip inside the window needs a VLC runtime. Trimming never does: every trim, every audio job and every other feature uses the bundled FFmpeg and works on a PC with no VLC on it at all.

**If VLC is already installed**, Middle-Trimmer uses that copy in place. It finds it through `TRIM_MIDDLE_VLC_DIR` if you set that variable, otherwise through the folder a previous setup recorded, the `SOFTWARE\VideoLAN\VLC` registry key, or the usual Program Files folders. Nothing is copied, installed, uploaded or reported.

**Both editions behave the same way.** If neither is found, the one-click "Set up video preview" installer fetches a private copy into your own profile; you can equally install VLC for Windows from videolan.org yourself, or point `TRIM_MIDDLE_VLC_DIR` at an existing copy. Without any of those the app says so, preview stays off, and every other feature keeps working.

What that installer downloads, when you choose it: the official portable VLC archive that the app is pinned to (version 3.0.24, about 79 MB) from `download.videolan.org`:

- the archive is verified against a pinned SHA-256 before anything is unpacked;
- a corrupt, truncated or unexpected download is discarded, and nothing is installed;
- the runtime is unpacked into a private folder, `%LOCALAPPDATA%\MiddleTrimmer\vlc`;
- the folder is only moved into place after the runtime loads successfully, so cancelling or failing leaves no half-installed copy behind.

That is an ordinary file download. The servers involved see your IP address, as any download server does; they receive no information about your files or your usage. We do not control those servers and they handle that data under their own policies. Nothing is installed system-wide: no installer runs, no registry key is written and no administrator rights are needed. VLC belongs to the VideoLAN project and is licensed under the GNU GPL; it is not part of the application in any edition and is fetched only at your request.

## Optional constant-quality encoding (x264)

The FFmpeg bundled with the Store package is an LGPL build, so it has no x264, and the
quality choices then become bitrate targets rather than a true constant quality. A download
fixes that, and only a download: this application never ships x264, because x264 is GPL and
the application is not.

**If the FFmpeg in use already has x264** — any full build beside the app, on `PATH`,
or one fetched earlier — a CRF number is a real constant quality and the button below
is not even shown.

**Both editions behave the same way.** When the encoder in use has no x264, "Use constant
quality" appears in the status row and offers the pinned GPL build from
`BtbN/FFmpeg-Builds` (version `n8.1.3-35-gf1acb0a266`, about 86 MB). You can equally install
any FFmpeg build with x264 yourself and point `TRIM_MIDDLE_FFMPEG` at it.

What that installer downloads, when you choose it:

- the archive is verified against a pinned SHA-256 before anything is unpacked;
- a corrupt, truncated or unexpected download is discarded, and nothing is installed;
- the runtime is unpacked into a private folder, `%LOCALAPPDATA%\MiddleTrimmer\ffmpeg-gpl`
  (`TRIM_MIDDLE_FFMPEG_GPL_ROOT` moves it);
- the folder is only moved into place after the unpacked FFmpeg reports `libx264`, so
  cancelling or failing leaves no half-installed copy behind.

That is an ordinary file download. The servers involved see your IP address, as any download
server does; they receive no information about your files or your usage. We do not control
those servers and they handle that data under their own policies. Nothing is installed
system-wide: no installer runs, no registry key is written and no administrator rights are
needed. That build belongs to the FFmpeg-Builds project and is licensed under the GNU GPL; it
is not part of the application in any edition and is fetched only at your request.

## Network requests

- **Default use, either edition:** none. Trimming a video makes no network request at all - the Store package uses its bundled FFmpeg, and the direct-download build uses the FFmpeg beside the app or on PATH.
- **If you run the AI setup:** the Python/PyPI/PyTorch downloads described above, plus one download of the model checkpoint from Hugging Face on first use of the AI mode.
- **If you use an AI environment you configured yourself:** only the Hugging Face checkpoint download on first use, as described above.
- **If you run the video-preview setup:** one download of the pinned VLC archive from `download.videolan.org`, as described above. Playing a clip afterwards makes no network request.
- **If you run the constant-quality setup:** one download of the pinned GPL FFmpeg archive
  from the `BtbN/FFmpeg-Builds` GitHub release, as described above. Encoding afterwards makes
  no network request.

There are no other outbound connections: no update check, no license check, no analytics endpoint, no advertising, no third-party SDK.

## Local storage

- `%APPDATA%\MiddleTrimmer\config.json` — the small settings file written on first use. It records your preferences (for example the last-used folders and options) and which optional runtimes, if any, are configured: the `ai_python` key holds the AI environment's interpreter, `vlc_dir` holds the video-preview runtime folder and `ffmpeg_gpl_dir` holds the optional x264 FFmpeg folder. It contains no personal information beyond the folder paths you chose, and deleting it resets the application to defaults.
- `%USERPROFILE%\.middle-trimmer\ai-env\` — the optional AI environment described above, including the downloaded model checkpoint (a few hundred MB). This folder exists only if you opted into AI noise reduction. It is deliberately **outside** the application's own folders and, in the Microsoft Store copy, outside the package's storage: this is what makes it survive an update, a reinstall or an uninstall. `TRIM_MIDDLE_AI_ROOT` moves it elsewhere in either edition.
- `ai-setup.log`, in the folder that holds that environment — a short transcript of the last **Set up AI noise reduction** attempt: the commands run, their exit codes and the tail of their output. It is overwritten on every attempt, capped in size, and contains only what those commands printed.
- `%LOCALAPPDATA%\MiddleTrimmer\vlc\` — the optional video-preview runtime described above, which takes noticeably more space on disk than the ~79 MB download. This folder exists only if you ran the video-preview setup.
- `%LOCALAPPDATA%\MiddleTrimmer\ffmpeg-gpl\` — the optional x264 FFmpeg described above, about 200 MB unpacked. This folder exists only if you ran that setup.

The application also reads the media files you open, and writes trimmed output and temporary working files to the folders involved in that operation. Temporary files are removed when the operation finishes.

The application does not write to the Windows registry.

## Removing the optional runtimes

You are never locked in. Each optional runtime is a folder you can delete, which the application's own help text documents:

- **AI noise reduction:** delete `%USERPROFILE%\.middle-trimmer\ai-env` (or whatever folder `TRIM_MIDDLE_AI_ROOT` points at). Note that uninstalling the application does not do this for you — the folder is outside the application's own storage on purpose, precisely so that reinstalling does not mean downloading it again — so removing the space is a manual step. Afterwards `AI MossFormer2` is simply unavailable again.
- **Video preview:** delete `%LOCALAPPDATA%\MiddleTrimmer\vlc`. Afterwards preview uses a VLC you installed yourself if one is present, and otherwise offers to set one up again.
- **Constant-quality encoding:** delete `%LOCALAPPDATA%\MiddleTrimmer\ffmpeg-gpl`. Afterwards
  the CRF choices fall back to the bundled encoder's bitrate estimate, and the button offers
  the download again.

Optionally delete `%APPDATA%\MiddleTrimmer\config.json` as well, which clears both recorded paths and resets the application to defaults.

Nothing else depends on either folder: trimming, manual mode and every other feature keep working, because they use the bundled FFmpeg and nothing else.

## Third-party software

The Microsoft Store package contains an unmodified, LGPL-licensed FFmpeg build (version `n8.1.3-35-gf1acb0a266`, from BtbN/FFmpeg-Builds) that runs as a separate local process. It is used only to read and write your media files and makes no network requests. Its licence and full attribution travel with the package as `third-party\FFmpeg-LICENSE.txt` and `third-party\FFmpeg-NOTICE.md`.

VLC is separate again and ships with no edition of Middle-Trimmer. When you install VLC yourself, or the direct-download build fetches the official portable archive at your request, it is the VideoLAN build, licensed under the GNU General Public License, and it runs as a separate local process to draw the preview. The VideoLAN project publishes that build and its source code.

x264 is separate again and ships with no edition of Middle-Trimmer. When a full GPL FFmpeg is
already in use, or you fetch the pinned build at your request, it is the FFmpeg-Builds build,
licensed under the GNU General Public License, and it runs as a separate local process to
read and write your videos. The FFmpeg-Builds project publishes that build and its source
code.

## Children

Middle-Trimmer is a video utility and is not directed at children. It collects no information from anyone, including children.

## Changes

If this policy changes, the revised version will be posted at this address with a new "last updated" date.

## Contact

Questions: use the support instructions on the download page.
