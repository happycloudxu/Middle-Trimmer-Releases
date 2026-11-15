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
| **`MiddleTrimmer.exe`** — start here | 14.7 MiB (15,449,737 bytes) | **The application.** Single Windows executable with Python, Tk and drag and drop inside: no installer, no Python install. Double-click it, then drop a video on the window |

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
| `MiddleTrimmer.exe` (v1.6.7) | 15,449,737 | `580f5f0acb904141fca2ec32431c04a35451cfa2dd4cdfcec076670e84443624` |

The same checksum is repeated in the release notes, so a download can be verified against
the page it came from.

## What's new

**1.6.7** puts a player inside the window. The drop zone becomes one as soon as a clip is
loaded — a draggable seek bar, a `0:03.4 / 0:08.0` readout, a play button that pauses and
resumes in place, and a close cross that puts the drop zone back — so you can watch what you
are about to cut without leaving the app. **Start** and **End** each gain a **Use now**
button that takes the current playhead position instead of a typed time. *Audio processing*
can now run over the whole video rather than only the kept section, and the main button
relabels itself to say what the next press will do — **Trim video**, **Reduce noise**,
**Change volume** or **Reduce noise + volume**. The SECTION card moved beside the drop zone,
the window is about 50 px shorter, and the bottom buttons line up in one row. A trim now
writes to a temporary file and renames it only when the job finishes, so Cancel or a closed
window can no longer leave a truncated file that looks complete. **1.6.6** fixes the
optional AI noise reduction on loud material: when the audio peaked at full scale, the
stereo-to-mono downmix handed to MossFormer2 reached +3 dBFS and the pass aborted with
*"Mono downmix reaches full scale; AI input would clip. Lower the source level first."* The
downmix is now fed to the model about 1 dB below full scale and the measured gain restores
the level afterwards, so those files trim instead of failing, and a failure leads with the
real reason rather than *"command failed (…python.exe):"*. **1.6.5** closed the empty band
that used to sit under the drop area, and renamed the window and taskbar entry to **Middle
Trimmer - Cut Video Middle**. **1.6.4** made trimming work with whatever FFmpeg you already
have. The [release notes](../../releases/latest) have the current version in full.

## Privacy

Middle Trimmer collects nothing. There is no account, no telemetry, no analytics, no
advertising and no update ping, and the application makes no network requests of its own:
everything it does happens on your PC with the local `ffmpeg`. Videos you trim are never
uploaded.

## Issues and support

This repository has no issue tracker for the application source. Report problems through
whoever supplied your copy of the build.

Trim only content you are authorized to copy.