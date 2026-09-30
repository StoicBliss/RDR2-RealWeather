# RealWeather for Red Dead Redemption 2

[![Red Dead Redemption 2](https://img.shields.io/badge/Game-Red%20Dead%20Redemption%202-red.svg)](https://www.rockstargames.com/reddeadredemption2)
[![ScriptHookRDR2 V2](https://img.shields.io/badge/ScriptHookRDR2-v2.0+-orange.svg)](https://www.nexusmods.com/reddeadredemption2/mods/1472)
[![Platform](https://img.shields.io/badge/Platform-PC%20%28x64%29-lightgrey.svg)]()
[![Language](https://img.shields.io/badge/Language-C%2B%2B20-00599C.svg)]()
[![Version](https://img.shields.io/badge/Version-1.0.2-green.svg)](CHANGELOG.md)

**RealWeather** seamlessly synchronizes Red Dead Redemption 2's atmosphere, weather, sky, temperature, and in-game clock with live meteorological conditions from **any location in the world**.

Whether you want the frontier to reflect rainy London, sunny Los Angeles, snowy Tokyo, or the weather in your own hometown, RealWeather brings real-world conditions into RDR2 with smooth, immersive, and authentic weather transitions.

The mod uses live meteorological data to dynamically translate real-world conditions into RDR2's native weather system, while optional 1:1 real-time clock synchronization keeps the in-game time aligned with the real world.

With asynchronous background networking and optimized weather updates, RealWeather is designed to run seamlessly without noticeable FPS drops or gameplay stuttering.


---

## Key Features

- **Pure Native ASI**:
  - Compiles directly to `RealWeather.asi`.
  - Zero dependencies on .NET / CLR runtimes.
  - Links directly to `ScriptHookRDR2.lib` (from the ScriptHookRDR2 V2 SDK) and `winhttp.lib`.

- **Worldwide Live Weather Synchronization**:
  - **Open-Meteo Integration**: 100% free, zero API key required, with automatic worldwide geocoding.
  - **OpenWeatherMap Integration**: Optional API key support.
  - **Auto Provider Mode**: Uses OpenWeatherMap if an API key is specified; otherwise seamlessly uses Open-Meteo.
  - **Asynchronous Background Networking**: Network queries execute via WinHTTP on background worker threads, guaranteeing zero framerate drops or game stuttering.

- **Temporary Game Session Weather Cache (30 Real-Minute Cycle)**:
  - `SESSION_CACHE = 1` and `CACHE_DURATION_MINUTES = 30`: fetches weather when your game session begins and holds it in memory for that single session.
  - Automatically refreshes the weather after 30 real-world minutes of gameplay, replacing frequent 300s polling with an optimal session cycle.
  - Session-scoped lifecycle: cleans up on startup and exit so each game session starts fresh.
  - Zero background internet requests during normal gameplay, with a 15-second local in-engine persistence lock.
  - Press `Right-Control + R` anytime to force-fetch fresh weather and reset the timer.

- **Dynamic Real-Time Lore Location Auto-Detection**:
  - When `LORE_LOCATION_NAME = Auto`, the mod dynamically tracks where the player is currently exploring on the RDR2 map (e.g. *Valentine, Saint Denis, Rhodes, Blackwater, The Heartlands, Big Valley*, etc.).
  - Uses native zone type lookups (`ZONE::_GET_MAP_ZONE_AT_COORDS` `0x43AD8FC02B429D33`) with radial and bounding-box geometry fallbacks.
  - The weather synced and displayed continues to reflect your target `LOCATION =` (e.g. Valentine, London, New York), while the on-screen alert banner dynamically shows your real-time in-game location!

- **All 22 RDR2 Weather Types Mapped**:
  - `Sunny`, `HighPressure`, `Clearing`, `Overcast`, `OvercastDark`, `Fog`, `Misty`, `Drizzle`, `Shower`, `Rain`, `Thunder`, `Thunderstorm`, `Hurricane`, `Hail`, `Sleet`, `Snowlight`, `Snow`, `SnowClearing`, `Blizzard`, `Whiteout`, `GroundBlizzard`, `Sandstorm`.

- **Smooth In-Engine Weather Blending**:
  - Native weather transitions using `MISC::SET_WEATHER_TYPE` over customizable durations (default: 15s).

- **1:1 Real-World Time Synchronization**:
  - In-game clock synchronization with `CLOCK::PAUSE_CLOCK(true)` and `CLOCK::SET_CLOCK_TIME`.
  - Time advances 1:1 with real-world seconds.
  - Persistent `.tzcache` file for instantaneous, accurate time upon game startup.
  - Supports 12-hour (AM/PM) and 24-hour formats.

- **Story Mission & Cutscene Protection**:
  - 500ms debounced detection of `MISC::GET_MISSION_FLAG`.
  - Automatically yields weather and lighting control to Rockstar's story missions and cinematics.
  - Smoothly restores live weather once missions conclude.

- **Western Subtitle HUD & Audio**:
  - Subtitle banner alerts using `UILOG::_UILOG_SET_CACHED_OBJECTIVE` & `_UILOG_PRINT_CACHED_OBJECTIVE`.
  - Radar-aware presentation and peeking support.
  - Subtle frontend audio cues.

- **Hot-Reloading Configuration**:
  - Modify `RealWeather.ini` at any time while the game is running—the mod automatically reloads your settings within 2 seconds.

---

## Installation

1. Install [**ScriptHookRDR2 V2**](https://www.nexusmods.com/reddeadredemption2/mods/1472) (`ScriptHookRDR2.dll` and `dinput8.dll`) in your main RDR2 game folder (where `RDR2.exe` is located).
2. Copy `RealWeather.asi` and `RealWeather.ini` directly into your main RDR2 game folder:
   ```
   <Red Dead Redemption 2>\RealWeather.asi
   <Red Dead Redemption 2>\RealWeather.ini
   ```

---

## Default Controls

| Action | Shortcut | Description |
| :--- | :--- | :--- |
| **Toggle Weather Mod** | `Right-Ctrl + W` | Enable or disable live weather overrides |
| **Toggle Real-Time Clock** | `Right-Ctrl + T` | Enable or disable 1:1 real-time clock synchronization |
| **Immediate Refresh** | `Right-Ctrl + R` | Force an instant weather and time re-synchronization |
| **Preview Notification** | `Right-Ctrl + O` | Trigger an immediate in-game test of your weather banner |

*All hotkeys can be customized in `RealWeather.ini` using either hex codes (e.g. `0x52`) or friendly key names (e.g. `F10`, `R`, `Ctrl`).*

---

## Download & Releases

Download the latest compiled release from [GitHub Releases](https://github.com/StoicBliss/RDR2-RealWeather/releases/latest).

Each release includes:
- `RealWeather.asi` (native 64-bit ASI plugin)
- `RealWeather.ini` (default configuration)
- `RealWeather_RDR2_ASI_Release.zip` (complete release archive)
## Author & Credits

- Developed by **StoicBliss** ([@StoicBliss](https://github.com/StoicBliss))
- Built on the native C++ SDK for [**ScriptHookRDR2 V2**](https://www.nexusmods.com/reddeadredemption2/mods/1472) by **kepmehz** (based on the foundational architecture by **Alexander Blade**)
- Meteorological data provided by [Open-Meteo](https://open-meteo.com/) and [OpenWeatherMap](https://openweathermap.org/)

