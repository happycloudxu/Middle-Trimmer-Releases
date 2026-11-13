# Middle Trimmer — downloadable builds

**This repository hosts released builds only. It contains no source code.** The Middle
Trimmer source lives in a private repository and is not published here, and nothing in
this repository is imported, compiled or needed by the application.

Cut a section out of the middle of a video — or keep only a middle section — by dragging a
file onto a window or with one command. Removing a range deletes it and splices the head and
tail back together; *keep only this part* does the opposite. Both modes keep the audio, work
on videos with no audio track, and stay frame-accurate because the default path re-encodes.

It is a thin wrapper around `ffmpeg`/`ffprobe`: no account, no cloud step, no upload.

## Which file do I download?

Get the executable from the [latest release](../../releases/latest).

| Asset | Size | What it is |
|---|---|---|
| **`MiddleTrimmer.exe`** — start here | 15.5 MiB (16,279,094 bytes) | **The application.** Single Windows executable with Python, Tk and drag and drop inside: no installer, no Python install. Double-click it, then drop a video on the window |

`MiddleTrimmer.exe` does **not** carry `ffmpeg`: a full ffmpeg build is far larger than the
app. Put `ffmpeg.exe` and `ffprobe.exe` next to the executable to keep it portable, or make
them findable on `PATH` (`winget install Gyan.FFmpeg`). The app says so in its status line
when they are missing, and `TRIM_MIDDLE_FFMPEG` / `TRIM_MIDDLE_FFPROBE` override the lookup.

Requires Windows. The executable is unsigned (there is no code-signing certificate), so
SmartScreen may show **"Windows protected your PC"** on first run: choose **More info → Run
anyway**. A matching SHA-256 proves the file left GitHub intact; it does not prove who built
it and does not suppress that prompt.

## Install

1. Download `MiddleTrimmer.exe` — single file, no Python needed.
2. Run it from any folder. It writes nothing beside itself; the only new files are the
   trimmed videos you ask for.
3. If **FFmpeg was not found** appears in the status line, install FFmpeg or put
   `ffmpeg.exe` and `ffprobe.exe` next to the executable.

Then drag a video onto the window (or press **Browse…**), type the **Start** and **End** of
the section (`90`, `1:30` or `01:02:03.5`), and press **Trim**. Leave *Keep only this part*
unticked to delete the section, or tick it to keep just that part. **Quality** picks High
(CRF 16), Balanced (CRF 20, default) or Smaller file (CRF 26); **Fast (no re-encode)** is
near-instant but moves the cut forward to the next keyframe, so a little more than requested
is removed.

**Help** (`F1`) opens the built-in manual, and **About** shows the version, author and
copyright of the build you are running.

## Checksums

Every asset is byte-identical to the file that was verified before release. Confirm after
downloading:

```powershell
Get-FileHash .\MiddleTrimmer.exe -Algorithm SHA256
```

```cmd
certutil -hashfile MiddleTrimmer.exe SHA256
```

| Asset | Bytes | SHA-256 |
|---|---|---|
| `MiddleTrimmer.exe` (v1.4.0) | 16,279,094 | `37d2a697ba3bc786606707609a270c23481645699d5fbed921cfbdd3b1043a65` |

The same checksum is repeated in the notes for each release, so a download from any version
can be verified against its own release page.

## What's new

**1.4.0** credits the studio instead of a personal name: **Author: Dr. Xu's Studio** appears
in the window's version line, the **About** dialog, the built-in manual, `--version` and the
file properties Windows shows for the executable (`CompanyName`, `LegalCopyright`). The
version was bumped so this rebuilt binary can be told apart from 1.3.0. Trimming, the
controls and the command-line options are unchanged. **1.3.0** added the **About** dialog and
renamed the executable to `MiddleTrimmer.exe` (it was `VideoTrimmer.exe` before), and removed
the repository link from the app's own UI. The [release notes](../../releases/latest) have
the full list.

## Privacy

Middle Trimmer collects nothing. There is no account, no telemetry, no analytics, no
advertising and no update ping, and the application makes no network requests of its own:
everything it does happens on your PC with the local `ffmpeg`. Videos you trim are never
uploaded.

## Issues and support

This repository has no issue tracker for the application source. Report problems through
whoever supplied your copy of the build.

Trim only content you are authorized to copy.
