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
| **`MiddleTrimmer.exe`** — start here | 14.8 MiB (15,539,149 bytes) | **The application.** Single Windows executable with Python, Tk and drag and drop inside: no installer, no Python install. Double-click it, then drop a video on the window |

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
unticked to delete the section, or tick it to keep just that part. **Quality** picks Visually
lossless (CRF 18), Balanced (CRF 20), Smaller file (CRF 26, the default) or Smallest file
(CRF 30); **Fast (no re-encode)** is
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
| `MiddleTrimmer.exe` (v1.6.17) | 15,539,149 | `4643511b42c6e5b68d39f6d1fd3af0f636b41225b1c2b2358d5b5de74d54bc4c` |

The same checksum is repeated in the release notes, so a download can be verified against
the page it came from.

## What's new

**1.6.17** makes a jump in the seek bar land in about half a second instead of seconds. A player
has to decode forward from the keyframe before the jump, and the encoders were left to choose their
own keyframe spacing: the LGPL FFmpeg inside the Store package falls back to `libopenh264`, which
then writes a single keyframe at the very start of the file, so jumping into a two-minute 4K trim
decoded the whole file first — fifteen seconds before the picture came back. Every encoder is now
told to write a keyframe every two seconds, the interval following the frame rate, which brought
that jump down to about 0.6 s and one at 15 s from 1.97 s to 0.50 s, and both the preview player
and the main player benefit. A **Fast** stream copy still keeps the source's own spacing, so
re-trim without it to get the denser keyframes. No Store package ships with 1.6.17 either; the
signed MSIX from **1.6.10** stays the current Store edition.

**1.6.16** makes the progress area say what the job is doing. The line under the bar kept reading
*Measuring the sound level...* for a whole encode, because the loudness measurement before it
reports no progress and the encode reported only its clock, and the bar filled to 100 % while the
file was still being written out — a running job looked finished. The encode now names itself, and
names the corrected gain when the AI pass retries, and the bar stops just short of the end until
the file is in place.

**1.6.15** keeps the files a VLC repair could not read. A folder inside the old preview runtime
that Windows refuses to list was skipped without a word, so the copy looked complete and the old
folder was deleted with its contents; an incomplete carry-over now keeps the previous copy and
the setup dialog says where it is. A video whose timestamps do not start at zero reports its real
length: an MKV stamped to begin at 10 s with a subtitle track running to the end was measured as
17 seconds instead of 7, so the Start and End boxes, the slider and the *past the end* check now
use the container's own end times for Matroska and WebM (MP4 and MPEG-TS are unchanged). **Trim**
waits only for the newest selection instead of for any read still running, so dropping a second
file while the first sits on a sleeping drive no longer disables the button, and the Store
packager accepts `-Version 1.6.15.0` as naming the same version as 1.6.15. No Store package ships
with 1.6.15 either; the signed MSIX from **1.6.10** stays the current Store edition.

**1.6.14** stops a window from refusing to close. With the optional AI setup running — or its
dialog just gone — the close button raised an internal error before the window was destroyed, and
every later close was ignored as well; closing now cancels the setup, tolerates a dialog that is
already gone and always finishes. The manual says what actually happens to an existing output: it
is replaced after the **Replace it?** prompt, not kept. Fast mode no longer offers `.m4v` codecs the
ipod muxer cannot write (HEVC, AV1 and MPEG-2 used to pass the check and then die inside ffmpeg with
*Could not find tag for codec*), an impossible output names its reason instead of reporting "no
keyframe found", and a fast cut more than an hour into a long recording is no longer refused because
the keyframe search reads the file's own duration. Cancelling — Ctrl+C, or closing the window — now
terminates the encoder instead of leaving it writing a file nobody is waiting for, a missing output
folder is reported by name instead of ending in a traceback, and a path containing an apostrophe no
longer breaks the fast-mode concat list. No Store package ships with 1.6.14 either; the signed MSIX
from **1.6.10** stays the current Store edition.

**1.6.13** makes fast mode cut where you asked. A stream copy can only begin at a keyframe, and
the app used to treat every frame the probe returned as one, so on a VP9 clip a cut at 2.5 s
snapped to a non-keyframe and the copied piece still contained the whole section; the real
keyframe flag is read now, each copied piece is checked to start on one, and a boundary only ever
moves forward. Keyframe choice and the seek also share one clock measured from the earliest
stream, so a video that starts at 1.32 s while its audio starts at 0 no longer splices 1.32 s
early, and a length is measured as a length, so an MPEG transport stream that starts at 1.44 s
keeps its last 34 frames. VP9 in MP4 may be copied again, an MPEG-2 program stream that the
intermediate cannot be written to retries in MPEG-TS, repairing the video preview keeps your own
files (a document beside `vlc.exe`, a `plugins\user-plugin.ini`) and accepts a hand-unpacked VLC,
the window reads a file off the Tk thread and holds **Trim** while a new selection is loading, and
a file that was saved but failed its read-back check is reported as saved instead of as an export
failure.

**1.6.12** makes volume a choice of three (leave it alone, normal speech level at -16 LUFS, or a
number you type), gives each technical control a link that explains it in plain words, extends
Quality from *Visually lossless* (18) to *Smallest file* (30) with 26 as the default, adds a
**Reset** for the section, and moves VLC's start, stop and release calls to the player's own thread
so closing Preview or the drop-box player no longer freezes the window. Every job writes `stage:`
lines and `logs\crash.log` records native crash stacks.

Earlier releases: **1.6.11** cleans the audio of the part you keep instead of the whole file, and it stops re-encoding
the picture when nothing is cut. The optional AI noise reduction used to run over the whole source
before trimming, so cutting two minutes out of a 75-minute recording paid for almost an hour of
inference and then threw nearly all of it away: the 60-second blocks the trim discards are now
copied through instead of being sent to the model, and that job went from 14 minutes 32 seconds to
2 minutes 19 seconds, leaving the 1080p encode as the rest. A volume or noise job with nothing cut
now copies the video stream and re-encodes only the audio, so a **+20 dB** boost no longer doubles
the file or quietly alters the picture. The progress bar advances during inference and shows a
percentage with an elapsed and remaining estimate, so a long job no longer looks stuck, and
`%LOCALAPPDATA%\MiddleTrimmer\logs\gui.log` records every start, finish, cancel and uncaught error:
a window that vanishes without a message is no longer a mystery. AI setup also decodes the common
Windows status codes (`0xC0000135` *a file it needs was not found*, `0xC0000022` *Windows blocked it
from starting*) and names the remedy instead of showing a bare number. There was no Store package
for that build either; the signed MSIX from **1.6.10** stays the current Store edition.

**1.6.10** put the AI environment under the packaged app's own folder and
reported a setup that dies before it can speak; **1.6.9** restored both setup buttons in the Store
edition and made Cancel stop the download or pip cleanly; **1.6.8** set up the video preview itself
(official portable VLC 3.0.24 for Windows x64, about 79 MB, SHA-256 checked before anything is
unpacked, into `%LOCALAPPDATA%\MiddleTrimmer\vlc`), drew the playback strip with anti-aliased
shapes, added a proper **About** window with the version, edition, capabilities and a **Copy
details** button, and merged `config.json` instead of replacing it so setting up one optional
runtime no longer forgets the other. **1.6.7** put a player inside the window — a draggable seek bar, a
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

The checksums above describe the current build. Older builds may still be listed on the
[release page](../../releases/latest); they are kept as published and are not updated.

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
