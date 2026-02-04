# SilenceRemover

A free, browser-based tool that automatically detects and removes silent sections from videos. No uploads required - all processing happens locally in your browser using FFmpeg WebAssembly.

![example gif](example.gif)

**Live Demo:** https://videosilenceremover.web.app

## Features

- **Automatic Silence Detection** - Analyzes audio waveform to find quiet sections
- **Interactive Waveform Editor** - Visual editing with WaveSurfer.js
- **Configurable Parameters** - Fine-tune detection sensitivity, duration, and padding
- **Manual Region Editing** - Add, resize, or delete regions by clicking the waveform
- **Multiple Export Options**
  - Direct video export via FFmpeg
  - Timeline XML export for Final Cut Pro / DaVinci Resolve
- **Auto-Save** - Regions are saved to localStorage and restored on page refresh
- **Keyboard Shortcuts** - Space to play/pause, variable playback speed (1x-2x)
- **Desktop App** - Available as a native app via Tauri (macOS, Windows, Linux)
- **Privacy-First** - All processing happens locally; no files are uploaded to any server

## How It Works

1. **Upload** - Drag and drop or select a video file
2. **Analyze** - Click "Analyze" to detect silent sections
3. **Adjust** - Fine-tune with parameter sliders or manually edit regions
4. **Export** - Download the processed video or export as timeline XML

### Parameters

| Parameter | Range | Description |
|-----------|-------|-------------|
| **Min Volume** | 0-30% | Audio below this threshold is considered silence |
| **Min Duration** | 0-1s | Minimum length for a region to be kept |
| **Pre Padding** | 0-1s | Extra silence added before each audible region |
| **Post Padding** | 0-1s | Extra silence added after each audible region |

## Installation

### Web Version (Development)

```bash
# Clone the repository
git clone https://github.com/jerryliuoft/SilenceRemover.git
cd SilenceRemover

# Install dependencies
npm install

# Start development server
npm run dev
```

Open http://localhost:3000 in your browser.

### Desktop Version (Tauri)

Requires [Rust](https://rustup.rs/) to be installed.

```bash
# Install dependencies
npm install

# Run in development mode
npm run tauri dev

# Build native app
npm run tauri build
```

Built apps are output to `src-tauri/target/release/bundle/`.

### Production Build

```bash
npm run build
```

The `dist/` folder can be deployed to any static host (Netlify, Vercel, Firebase, etc.).

## Tech Stack

- **Frontend Framework:** [SolidJS](https://solidjs.com/)
- **Build Tool:** [Vite](https://vitejs.dev/)
- **Styling:** [Tailwind CSS](https://tailwindcss.com/)
- **Audio Visualization:** [WaveSurfer.js](https://wavesurfer-js.org/)
- **Video Processing:** [FFmpeg WASM](https://github.com/ffmpegwasm/ffmpeg.wasm)
- **Desktop App:** [Tauri](https://tauri.app/)

## Browser Compatibility

- Chrome/Edge 89+ (recommended)
- Firefox 79+
- Safari 15+

**Note:** Chrome is recommended for best performance. File size is limited by browser memory (~2GB in Chrome).

## Project Structure

```
SilenceRemover/
├── src/
│   ├── App.tsx              # Main app component
│   ├── index.tsx            # Entry point
│   ├── index.css            # Tailwind + custom styles
│   └── components/
│       ├── VideoEditor.tsx       # Main editor layout
│       ├── Uploader.tsx          # File upload component
│       ├── VideoPlayer.tsx       # HTML5 video player
│       ├── VideoPlayerControls.tsx   # Playback controls
│       ├── SoundPlayer.tsx       # WaveSurfer waveform
│       ├── SilentConfigControls.tsx  # Parameter sliders
│       ├── SilentHelper.ts       # Silence detection algorithm
│       ├── VideoRender.tsx       # FFmpeg processing
│       └── TimelineExport.ts     # XML export for NLEs
├── src-tauri/               # Tauri desktop app config
├── package.json
├── vite.config.ts
└── tailwind.config.js
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

MIT License - see [LICENSE](LICENSE) for details.

## Acknowledgments

- [FFmpeg](https://ffmpeg.org/) for the powerful video processing capabilities
- [ffmpeg.wasm](https://github.com/ffmpegwasm/ffmpeg.wasm) for bringing FFmpeg to the browser
- [WaveSurfer.js](https://wavesurfer-js.org/) for the audio waveform visualization
