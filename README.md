<p align="center">
  <img src="QyuPipe.png" width="128" height="128" alt="QyuPipe Logo" />
</p>

# QyuPipe

> Read this in: **English** | [Bahasa Indonesia](README.id.md)

> **Lightweight YouTube Third-Party Client Optimized for 1:1 Aspect Ratio Retro Devices, QNX BlackBerry 10, and Android Runtime 4.3 (Jelly Bean API Level 18)**

---

## 📖 Project Overview

**QyuPipe** is an independent, lightweight third-party YouTube client built and optimized specifically for unique retro mobile hardware, primarily the **BlackBerry Q10** and **BlackBerry Classic (Q20)** running the **BlackBerry 10 OS (QNX Microkernel)** with its embedded **Android Runtime 4.3 (API Level 18)**.

Originating from the **notPipe 0.3** codebase, QyuPipe introduces comprehensive architectural overhauls: modern SSL/TLS handshake bypasses for legacy Android runtimes, strict Dalvik VM heap memory limits, Qualcomm Snapdragon hardware decoder stabilization, and a refined user interface designed specifically for square 720×720 screens and physical QWERTY keyboards.

---

## ✨ Core Features

### 1. 1:1 Aspect Ratio & Letterbox Subtitles
- The layout is engineered from the ground up for square 720×720 pixel displays.
- Videos render in the standard 16:9 aspect ratio at the top. The vertical letterbox space below is utilized for custom **Closed Caption (CC)** display, ensuring subtitles never obstruct the video frame.

### 2. Flexible Subtitle Selection (Subtitle Chooser)
- Robust parsing support for WebVTT, SubRip (.srt), and YouTube TimedText XML formats.
- **Smart Native Audio Detection:** Automatically detects the video's original spoken language and prioritizes native creator subtitles or original audio captions, avoiding rigid machine auto-translations.
- **Interactive Subtitle Chooser:** Accessible via the CC action button (tap for quick toggle, long-press to browse and select from all available manual and auto-generated language tracks).

### 3. Screen-Off Mode (Background Audio Playback)
- Battery-efficient background audio playback optimized for AMOLED displays (pure `#000000` black overlay turns display pixels completely off).
- Backed by a persistent **Foreground Service** and **Partial WakeLock** (`PowerManager.PARTIAL_WAKE_LOCK`), complying with QNX power management policies to keep CPU active and audio streaming when the physical power button is pressed.
- **Zero-Latency Transition:** Utilizes a seamless Overlay Mode that keeps the hardware video decoder stream running, preventing re-buffering, desync, or reconnection delays.

### 4. Rock-Solid Streaming & Legacy SSL Handshake Bypass
- Enforces single-stream MPEG-4 Muxed 360p (**itag 18**) combining H.264 Baseline Profile video and AAC-LC stereo audio in a single MP4 container.
- Routes media streams through clean HTTP reverse proxy endpoints (Invidious / Piped proxy) to eliminate the `SSLv3 alert handshake failure` common to Android 4.3 mediaserver when connecting to modern HTTPS CDNs.
- Eliminates resource-heavy fallback loops and includes automated stream recovery for `I/O connection timeout` (`-1004`).

### 5. Local Database Optimization & Pagination (Anti-OOM)
- Local storage (liked videos, watch history, and channel subscriptions) is migrated to indexed **SQLite** tables with indexing on the `added_at` timestamp column.
- Implements lazy-loading pagination (`LIMIT 20 OFFSET ?`) across feed and history views, preventing Dalvik VM *OutOfMemoryError* (OOM) crashes on memory-constrained devices.

### 6. Predictive Search (Debounced Keyword Suggestions)
- Fast auto-suggest query dropdown powered by public suggestion endpoints.
- **300 ms Debounce:** A Handler-based debounce mechanism waits for the user to pause typing, preventing network request flooding while typing on physical QWERTY keyboards.
- Proportional dark dropdown dialog ensures search suggestions do not block navigation controls.

### 7. Account Data Backup & Restore (.txt / JSON)
- Export and import local user data (subscribed channels and liked videos) to structured text/JSON files in device public storage.
- Ensures seamless data portability across APK upgrades and fresh installations without relying on cloud sync.

### 8. Streamlined Retro Navigation
- **One-Tap Share Button:** Instantly copies video URLs (`https://youtu.be/<id>`) to the Android system clipboard with toast feedback, bypassing unsupported third-party share sheets.
- **Pure Dark Placeholders:** Uses solid dark placeholders (`#000000` / `#121212`) for video thumbnails to eliminate white flashes and accelerate list scrolling.
- **Channel Profile View:** Direct navigation to channel details, avatars, subscription state, and video upload feeds.

---

## 🛠️ System Requirements & Technical Architecture

| Parameter | Technical Specification |
| :--- | :--- |
| **Target Hardware** | BlackBerry Q10 (SQN100-X), BlackBerry Classic (Q20), BlackBerry Passport, and legacy Android devices |
| **Host Operating System** | BlackBerry 10 OS (QNX Microkernel) |
| **Android Runtime Target** | Android 4.3 Jelly Bean (**API Level 18**) |
| **CPU Architecture** | `armeabi-v7a` (Qualcomm Snapdragon S4 Plus MSM8960 / Krait Dual-Core) |
| **Media Format** | MP4 Container, Video: H.264 (AVC) 360p, Audio: AAC Stereo 44.1 kHz |
| **Bitmap Memory Limit** | In-memory `LruCache` capped strictly at **12 MB** (`12 * 1024 * 1024` bytes) |
| **HTTP Engine** | OkHttp 3.12.13 (final LTS branch supporting Android 4.x / Java 7/8 bytecode) |

---

## 📂 Project Structure

```
Notpipe Revamp/
├── app/
│   ├── src/main/
│   │   ├── java/com/notpipe/bbq10/
│   │   │   ├── db/              # SQLite Database Helper & Schema Migrations
│   │   │   ├── model/           # Data Models (Video, Channel, StreamInfo, SubtitleTrack)
│   │   │   ├── network/         # ApiClient, HttpClientProvider, TLSSocketFactory, ImageLoader
│   │   │   ├── profile/         # Local Profile Management & Preset Avatars
│   │   │   ├── service/         # AudioPlaybackService (Background Audio & Partial WakeLock)
│   │   │   ├── subtitle/        # SubtitleParser (WebVTT, SRT, TimedText)
│   │   │   └── ui/              # MainActivity, PlayerActivity, ChannelActivity, SearchResultsActivity
│   │   ├── res/                 # Square 1:1 Layouts, Theme Drawables, Values
│   │   └── AndroidManifest.xml
│   └── build.gradle             # Build Config, ABI Splits & Dependencies
├── gradle/                      # Gradle Wrapper
├── build.gradle                 # Root Project Build Script
├── README.md                    # English Documentation (Default)
└── README.id.md                 # Indonesian Documentation
```

---

## ⚠️ Known Issues & Limitations

1. **Live Streams Not Supported:**
   - YouTube Live Streams cannot be played due to architectural limitations of the legacy native `MediaPlayer` in Android 4.3 (API 18), which lacks support for modern dynamic HLS chunking and DASH manifests without newer ExoPlayer frameworks.
2. **No YouTube Shorts Feature:**
   - Dedicated YouTube Shorts navigation and vertical reel feeds are intentionally omitted. The 9:16 vertical video format is fundamentally impractical for the BlackBerry Q10's 1:1 square display (720×720).
3. **Strict Resolution Lock to 360p (itag 18):**
   - Video streams are strictly locked to MP4 muxed 360p. This resolution matches the 720×720 viewport (scaled smoothly with hardware acceleration) and guarantees flawless Snapdragon S4 Plus hardware decoding without risking Dalvik VM heap exhaustion or excessive battery drain.
4. **No Google Account Sign-In:**
   - Official Google Account authentication is not supported due to the absence of Google Play Services on BlackBerry 10's Android Runtime. All subscription management, watch history, and liked videos operate independently via the offline SQLite database and text/JSON backup files.

---

## 🔨 Build Instructions

### Prerequisites
- **Java Development Kit (JDK):** JDK 8 or JDK 11 (JDK 8 / 1.8 recommended).
- **Android SDK Build Tools:** 28.0.3 with SDK Platform API 28 (configured for target runtime API 18).

### Compile Commands
Open a terminal in the root project directory and execute:

**On Windows:**
```powershell
.\gradlew.bat assembleDebug
```

**On Linux / macOS:**
```bash
./gradlew assembleDebug
```

### Output APK Locations
Upon a successful build (`BUILD SUCCESSFUL`), the output APK binaries are generated in:

1. **Optimized for BlackBerry Q10 (ARMv7):**
   ```
   app/build/outputs/apk/debug/app-armeabi-v7a-debug.apk
   ```
   *(Recommended: Leaner package size, compiled specifically for Snapdragon S4 ARMv7 instruction set).*

2. **Universal APK:**
   ```
   app/build/outputs/apk/debug/app-universal-debug.apk
   ```

---

## 📱 Installation on BlackBerry 10

> [!TIP]
> **For BlackBerry OS 10.2.1 and Higher:**
> Development Mode is **not** required! APK files can be installed directly through the native File Manager. Simply navigate to **Settings** -> **App Manager** -> **Installing Apps**, and enable the toggle for **"Allow installation of apps from other sources"**.

### Installation Steps:
1. **Transfer APK Binary:**
   - Connect your BlackBerry Q10 to your PC via USB cable, and copy `app-armeabi-v7a-debug.apk` to the internal storage or microSD card.
   - Alternatively, transfer the APK via browser download, Bluetooth, or Wi-Fi Sharing.
2. **Install via File Manager:**
   - Open the native **File Manager** app on your BlackBerry 10 device.
   - Locate `app-armeabi-v7a-debug.apk` and tap it.
   - Tap **Install** in the top-right corner of the screen.
3. **Launch Application:**
   - Once installation completes, the **QyuPipe** app icon will appear on your BlackBerry 10 home screen ready to launch.

---

## ⚖️ License & Acknowledgments

- This project is developed as a non-commercial, open-source preservation effort for retro BlackBerry 10 devices.
- Special thanks to the original [notPipe](https://github.com/gohoski/notPipe) project by gohoski, as well as the Invidious and Piped public API communities for their alternative YouTube backend services.
