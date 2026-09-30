# 🔔 Ringtone Maker

Turn an iPhone screen recording (or any video/audio clip) into a real iPhone ringtone — entirely in your browser. No app install, no upload to a server, no GarageBand required.

**[Open the app](https://anthonymancino2.github.io/Ringtones/)** *(enable GitHub Pages in repo Settings → Pages → source: `main` / root, if it isn't live yet)*

## What it does

1. **Pick a video or audio file** — including screen recordings straight from your Photos app.
2. **Trim it** on a live, glowing waveform. Drag the region or its handles; ringtones are capped at 30 seconds, which is Apple's hard limit.
3. **Convert** the selection to AAC audio in an `.m4r`/`.m4a` container, using `AudioEncoder`/WebCodecs when available, with an automatic `MediaRecorder` fallback for browsers that don't support it.
4. **Save it** — either through the native iOS share sheet (AirDrop, Files, Messages, Mail) or as a direct download.

Everything happens on-device. The file never leaves your phone or computer.

## Why isn't there a "Set as Ringtone" button?

Because Apple doesn't allow it. There is no web API that lets a Safari page reach into iOS system settings and change your ringtone — that's a deliberate sandboxing restriction, not a limitation of this app. No browser-based tool, on any platform, can do it either.

What this app *does* get you all the way to: a correctly formatted, correctly named `.m4r` file. The last step — actually applying it — has to happen on-device, outside the browser, as Apple designed it:

- **iPhone running the current iOS ("Use as Ringtone" via Files):** Save the file, open it from the **Files** app, tap **Share → Use as Ringtone**. It's added straight to *Settings → Sounds & Haptics → Ringtone*.
- **Older iOS, or if that option isn't there:** use the free **GarageBand** app (drag the saved file into a track, then *Share → Ringtone → Export*), or plug into a Mac and drag the `.m4r` onto your device in Finder.

Both paths are spelled out step-by-step in the app itself once your ringtone is ready.

## Browser support

Works best in current **Safari on iPhone/iPad** and desktop **Chrome/Edge/Safari**. Needs:
- `AudioContext.decodeAudioData` (universal) to read the audio out of your video.
- `AudioEncoder` (WebCodecs) for fast, high-quality AAC encoding — falls back automatically to `MediaRecorder` if unavailable.
- `navigator.share` with file support for the one-tap "Save to my iPhone" flow — falls back to a plain download link otherwise.

## Running it locally

It's a single static HTML file with no build step:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## Tech notes

- Single-file app (`index.html`) — no framework, no bundler.
- Audio decode/trim/waveform: Web Audio API + Canvas.
- Encoding: [`mp4-muxer`](https://github.com/Vanilagy/mp4-muxer) (loaded from jsDelivr) muxes raw AAC frames from `AudioEncoder` into a valid `.m4a`/`.m4r` container.
- No backend, no analytics, no data collection.

## License

MIT — see [LICENSE](LICENSE).
