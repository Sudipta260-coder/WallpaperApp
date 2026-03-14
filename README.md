# Wallpaper App

Wallpaper App is a macOS AppKit desktop utility that plays videos and animated images behind your desktop icons as a live wallpaper.

It includes a floating control center, a status bar menu, launch-at-login support, audio control, battery saver controls, and automatic `WEBM -> MP4` conversion for files that macOS cannot play natively.

## Highlights

- Live wallpaper window placed between the system wallpaper and Finder desktop icons
- Hardware-accelerated video playback with `AVFoundation` and `AVPlayerLayer`
- Supports multiple displays and all Spaces
- Optional Dock icon or status-bar-only mode
- Launch at login support
- Audio on/off control
- Battery saver controls with optional automatic battery mode
- Automatic restore of the last selected wallpaper
- Animated `GIF` and `WEBP` wallpaper support
- Automatic `WEBM` import conversion using `ffmpeg`

## Supported Media

### Native playback

- `mp4`
- `mov`
- `m4v`

### Animated image playback

- `gif`
- `webp`

### Imported with automatic conversion

- `webm`

`WEBM` files are converted to an app-managed `.mp4` on import, then played with the normal macOS video pipeline.

## Features

### Control Center

The main window includes:

- `Choose Video` / `Change Video`
- `Reveal File`
- `Clear Wallpaper`
- `Show Dock icon`
- `Start at login`
- `Play audio`
- `Pause on sleep or lock`
- `Battery saver`
- `Auto-enable on battery`

### Status Bar Menu

The status item includes quick access to:

- Open Control Center
- Choose Video
- Play Audio
- Battery Saver
- Clear Wallpaper
- Quit

### Playback Behavior

- The wallpaper window is borderless and ignores mouse events
- Playback appears behind desktop icons
- The wallpaper follows Spaces and desktops
- Playback pauses when the display sleeps or the session locks
- Battery saver can pause playback manually or automatically when on battery

## How `WEBM` Import Works

Some `WEBM` files, especially high-resolution `VP9` videos such as `3840x2160 @ 60fps`, are not playable through macOS `AVFoundation`.

When a selected file is a `WEBM` video:

1. Wallpaper App checks the file format on import
2. If the file is not natively playable, it is converted with `ffmpeg`
3. The converted `.mp4` is stored in:

```text
~/Library/Application Support/Wallpaper App/Converted Media/
```

4. The converted file is used for playback automatically
5. The original selected file path is still preserved in app state for restore/reimport behavior

Notes:

- Converted files are cached
- Large `4K` / `60fps` imports can take time
- Resolution and frame rate are preserved where possible
- Conversion is still a re-encode, so it is not mathematically lossless

## Requirements

- macOS 14.0 or later
- Xcode
- `xcodegen`
- `ffmpeg` for `WEBM` import support

Homebrew install example:

```bash
brew install xcodegen ffmpeg
```

## Build and Run

1. Generate the Xcode project:

```bash
xcodegen generate
```

2. Open the project:

```bash
open "Wallpaper App.xcodeproj"
```

3. Build and run the `Wallpaper App` scheme in Xcode.

## Usage

1. Launch the app
2. Open the Control Center
3. Click `Choose Video`
4. Select a supported file
5. The wallpaper starts behind your desktop icons

If you import a large `WEBM`, the app may show an importing state while conversion finishes.

## Launch at Login

Wallpaper App supports two startup paths:

- `SMAppService` when installed in a standard app location
- LaunchAgent fallback for local development builds

For local builds outside `/Applications`, launch-at-login uses a LaunchAgent plist in:

```text
~/Library/LaunchAgents/
```

## Project Structure

```text
Sources/
  AppDelegate.swift
  AppPreferences.swift
  ControlWindowController.swift
  DebugLog.swift
  PowerSourceMonitor.swift
  StartupManager.swift
  VideoConversionService.swift
  WallpaperWindow.swift
  Info.plist
project.yml
```

## Technical Notes

- UI layer: `AppKit`
- Video playback: `AVFoundation`
- Animated images: `ImageIO` + frame-based rendering
- Desktop placement: `CGWindowLevelForKey(.desktopIconWindow) - 1`
- Mouse passthrough: `ignoresMouseEvents = true`
- Multi-space persistence: `.canJoinAllSpaces`

## Known Limitations

- Native playback depends on macOS codec support
- `WEBM` playback is handled through import conversion, not direct playback
- Very large conversions can take noticeable time on first import
- Status-bar-only mode is intended for normal use, but Dock mode is easier during debugging

## Version

Current app version: `2.0`
