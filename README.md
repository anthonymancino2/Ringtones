# 🔔 Ringtone Maker

Turn an iPhone screen recording (or any video/audio clip) into a real iPhone ringtone — entirely in your browser. No app install, no upload to a server, no GarageBand required.

**[Open the app](https://anthonymancino2.github.io/Ringtones/)** *(enable GitHub Pages in repo Settings → Pages → source: `main` / root, if it isn't live yet)*

## What it does

1. **Pick a video or audio file** — a screen recording, or a video you've already saved from TikTok, YouTube, or anywhere else (`.mp4`/`.mov` work best; `.webm`, common for some YouTube downloads, isn't well supported on iPhone — convert to `.mp4` first if you hit that). This does **not** fetch or download anything from TikTok/YouTube itself — pick a file already on your device.
2. **Trim it** on a live, glowing waveform. Drag the region or its handles; ringtones are capped at 29 seconds — just under Apple's 30s hard limit, since a clip timed at exactly 30.0s can measure slightly over after encoding and get silently rejected. Optionally add a **fade in** and/or **fade out** (independent toggles, shared length slider, 0.2–3s) — the "Listen" preview plays the fade too, so you can dial it in before exporting.
3. **Convert** the selection to AAC audio in a proper `.m4r`/`.m4a` container using a bundled copy of [ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm) — the same encoder countless working "make an M4R" tools rely on, run entirely client-side. (Lighter browser-native paths — WebCodecs, then `MediaRecorder` — are kept as a fallback if ffmpeg.wasm can't load.)
4. **Save it** — either through the native iOS share sheet (AirDrop, Files, Messages, Mail) or as a direct download.

Everything happens on-device. The file never leaves your phone or computer.

There's also a dark mode toggle (top-left) — follows your system setting by default, or tap it to force light/dark regardless of system preference (remembered for next time).

## Why isn't there a "Set as Ringtone" button?

Because Apple doesn't allow it. There is no web API that lets a Safari page reach into iOS system settings and change your ringtone — that's a deliberate sandboxing restriction, not a limitation of this app. No browser-based tool, on any platform, can do it either.

What this app *does* get you all the way to: a correctly formatted, correctly named `.m4r` file. The last step — actually applying it — has to happen on-device, outside the browser, as Apple designed it:

- **iPhone running the current iOS ("Use as Ringtone" via Files):** Save the file, open it from the **Files** app, tap **Share → Use as Ringtone**. It's added straight to *Settings → Sounds & Haptics → Ringtone*.
- **Older iOS, or if that option isn't there:** use the free **GarageBand** app (drag the saved file into a track, then *Share → Ringtone → Export*), or plug into a Mac and drag the `.m4r` onto your device in Finder.

Both paths are spelled out step-by-step in the app itself once your ringtone is ready.

## Browser support

Works best in current **Safari on iPhone/iPad** and desktop **Chrome/Edge/Safari**. Needs:
- `AudioContext.decodeAudioData` (universal) to read the audio out of your video.
- WebAssembly + module Workers, to run the bundled ffmpeg.wasm encoder (broadly supported everywhere; falls back to `AudioEncoder`/WebCodecs, then `MediaRecorder`, if it can't load).
- `navigator.share` with file support for the one-tap "Save to my iPhone" flow — falls back to a plain download link otherwise.

The result screen shows a small debug line (encoder used, container structure, declared duration) — useful if a ringtone ever gets accepted into iOS's Ringtone list but won't actually play.

## Running it locally

It's a single static HTML file with no build step:

```bash
python3 -m http.server 8000
```

Then open `http://localhost:8000`.

## Tech notes

- `index.html` — no framework, no bundler; the only "build step" for the app itself is none.
- Audio decode/trim/waveform: Web Audio API + Canvas.
- Encoding: a self-hosted copy of [ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm) under `vendor/` (not loaded from a CDN — its worker script has further relative imports that break once cross-origin, so the whole module tree is served same-origin). Fallback paths: WebCodecs `AudioEncoder` + [`mp4-muxer`](https://github.com/Vanilagy/mp4-muxer), then `MediaRecorder`.
- No backend, no analytics, no data collection.

## License

MIT — see [LICENSE](LICENSE).
