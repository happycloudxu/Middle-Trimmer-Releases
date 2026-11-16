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
| **`MiddleTrimmer.exe`** — start here | 14.8 MiB (15,477,478 bytes) | **The application.** Single Windows executable with Python, Tk and drag and drop inside: no installer, no Python install. Double-click it, then drop a video on the window |

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
2. Run it from any folder. It writes nothing beside itself: settings go to
   `%APPDATA%\MiddleTrimmer\config.json`, and the two optional runtimes (AI noise
   reduction, VLC preview) live under `%LOCALAPPDATA%\MiddleTrimmer` if you set them up.
   The only other new files are the trimmed videos you ask for.
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
| `MiddleTrimmer.exe` (v1.6.8) | 15,477,478 | `eb8609eb64ee48baaf57f6b6180809748e797684dcd41fabf4e76b938dc758cc` |

The same checksum is repeated in the release notes, so a download can be verified against
the page it came from.

## What's new

**1.6.8** can set up its own video preview. When no VLC is found, the status line offers **Set up
video preview**: it downloads the official portable VLC 3.0.24 build for Windows x64 (about 79 MB,
SHA-256 checked before anything is unpacked), unpacks it into `%LOCALAPPDATA%\MiddleTrimmer\vlc`
and remembers that folder in `%APPDATA%\MiddleTrimmer\config.json`. No installer runs, no registry
key is written and no administrator rights are needed, and a cancelled or failed run leaves nothing
half-installed. The playback strip is now drawn with anti-aliased shapes, so the disc, the bar and
the glyphs stay smooth at any size. There is a real **About** window with the version, edition,
capabilities and the Windows version it is running on, plus a **Copy details** button for support
reports. Only one optional-runtime dialog can run at a time, `config.json` is merged rather than
replaced so setting up preview no longer forgets a configured AI environment (or the reverse), and
an unwritable settings file is reported in words instead of failing quietly.

Earlier releases: **1.6.7** put a player inside the window — a draggable seek bar, a
`0:03.4 / 0:08.0` readout, a play button that pauses and resumes in place, a close cross that puts
the drop zone back, and a **Use now** button beside Start and End that takes the playhead position.
*Audio processing* can run over the whole video rather than only the kept section, and the main
button relabels itself to say what the next press will do — **Trim video**, **Reduce noise**,
**Change volume** or **Reduce noise + volume**. A trim writes to a temporary file and renames it only
when the job finishes, so Cancel or a closed window cannot leave a truncated file that looks
complete. **1.6.6** fixed the optional AI noise reduction on loud material, where the stereo-to-mono
downmix reached full scale and the pass aborted; it is now fed to the model about 1 dB below full
scale. **1.6.5** closed the empty band under the drop area and renamed the window and taskbar entry
to **Middle Trimmer - Cut Video Middle**.

This repository keeps one release, the current build. Older builds are retired here, so the
[release page](../../releases/latest) and the checksums above always describe the same file.

## Privacy

Middle Trimmer collects nothing. There is no account, no telemetry, no analytics, no advertising
and no update ping, and trimming makes no network request at all: it runs the local `ffmpeg` and
that is the whole job. The only times the application touches the network are the two optional
runtimes you can ask it to set up — the AI noise-reduction environment and the VLC preview runtime
— and each one downloads only after you press the button and confirm. Videos you trim are never
uploaded. The full policy is [PRIVACY.md](PRIVACY.md).

## Issues and support

This repository has no issue tracker for the application source. Report problems through
whoever supplied your copy of the build.

Trim only content you are authorized to copy.
