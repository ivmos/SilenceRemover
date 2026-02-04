# CLAUDE.md

This file provides context for AI assistants working with this codebase.

## Project Overview

SilenceRemover is a browser-based video silence removal tool built with SolidJS and FFmpeg WebAssembly. Users upload videos, the app analyzes audio to detect silent sections, and exports a shortened video with silence removed.

## Architecture

### Frontend Framework
- **SolidJS** - Reactive UI framework (similar to React but with fine-grained reactivity)
- Uses `createSignal()` for state, `createEffect()` for side effects
- JSX with `.tsx` files

### Key Libraries
- **WaveSurfer.js** - Audio waveform visualization and region editing
- **@ffmpeg/ffmpeg** - FFmpeg compiled to WebAssembly for in-browser video processing
- **Tauri** - Desktop app wrapper (Rust-based, lighter than Electron)

### Data Flow
1. User uploads video → `Uploader.tsx`
2. Video displays in player → `VideoPlayer.tsx`
3. Audio extracted and displayed as waveform → `SoundPlayer.tsx`
4. User clicks Analyze → `SilentHelper.ts` processes audio data
5. Regions appear on waveform (WaveSurfer RegionsPlugin)
6. User exports → `VideoRender.tsx` uses FFmpeg to process video

## Key Files

| File | Purpose |
|------|---------|
| `src/App.tsx` | Root component, manages video state |
| `src/components/SilentHelper.ts` | Core silence detection algorithm |
| `src/components/VideoRender.tsx` | FFmpeg processing and export |
| `src/components/SoundPlayer.tsx` | WaveSurfer waveform setup |
| `src/components/VideoPlayerControls.tsx` | Play/pause, speed, region management |
| `src/components/SilentConfigControls.tsx` | Parameter sliders UI |
| `src/components/TimelineExport.ts` | XML export for video editors |
| `vite.config.ts` | Vite config with CORS headers for FFmpeg WASM |

## Silence Detection Algorithm

Located in `SilentHelper.ts`:

1. Get decoded audio data from WaveSurfer (`Float32Array`)
2. Find maximum volume level in the audio
3. Calculate threshold = `max_volume × (minVolume / 100)`
4. Iterate through samples, marking regions above threshold
5. Merge close regions (based on padding values)
6. Filter out regions shorter than `minDuration`
7. Apply pre/post padding to each region

## Common Commands

```bash
# Development
npm install          # Install dependencies
npm run dev          # Start dev server (http://localhost:3000)

# Production
npm run build        # Build to dist/
npm run serve        # Preview production build

# Desktop (Tauri)
npm run tauri dev    # Run desktop app in dev mode
npm run tauri build  # Build native installers
```

## Important Implementation Details

### FFmpeg WASM Requirements
- Requires CORS headers (`Cross-Origin-Opener-Policy`, `Cross-Origin-Embedder-Policy`)
- These are configured in `vite.config.ts`
- FFmpeg core is loaded from unpkg CDN (see `VideoRender.tsx:24`)

### WaveSurfer Plugins
The app uses WaveSurfer with the Regions plugin:
- Plugin is accessed via `ws.getActivePlugins()[1]` (index 1)
- Regions represent audible sections to keep
- Regions are stored in localStorage for persistence

### State Persistence
- Regions are saved to `localStorage` with key `"zones"`
- Format: `{ file: string, region: Region[] }`
- Last used slider values are also persisted

### Video Processing FFmpeg Command
```bash
ffmpeg -i video.mp4 \
  -vf "select='between(t,start1,end1)+between(t,start2,end2)+...',setpts=N/FRAME_RATE/TB" \
  -af "aselect='...',asetpts=N/SR/TB" \
  output.mp4
```

## Coding Conventions

- TypeScript with strict mode
- SolidJS component pattern: `const Component: Component<Props> = (props) => { ... }`
- Tailwind CSS for styling (utility-first)
- No semicolons enforced (but used inconsistently)
- Signal naming: lowercase (`[value, setValue]`)

## Testing

No test framework is currently configured. Consider adding Vitest for unit tests.

## Known Limitations

- File size limited by browser memory (~2GB in Chrome)
- FFmpeg WASM is slower than native FFmpeg
- Some video codecs may not be supported by browser FFmpeg
- Safari may have issues with certain WASM features
