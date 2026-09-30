# Changelog - RealWeather.asi for Red Dead Redemption 2

## [1.0.2] - 2026-09-29

### Added
- **Dynamic In-Game Lore Location Auto-Detection**:
  - `LORE_LOCATION_NAME = Auto`: The mod now automatically detects where the player is currently located in the game world in real-time.
  - Queries native `ZONE::_GET_MAP_ZONE_AT_COORDS` (`0x43AD8FC02B429D33`) across `TOWN`, `DISTRICT`, and `STATE` zone types.
  - Recognizes all RDR2 towns (Valentine, Saint Denis, Rhodes, Strawberry, Blackwater, Annesburg, Armadillo, Tumbleweed, Emerald Ranch, Van Horn, Wapiti, etc.) and territories/districts (The Heartlands, Bayou Nwa, Big Valley, Grizzlies, etc.).
  - Includes a radial and regional coordinate fallback system to guarantee accurate location naming everywhere on the frontier.
  - The weather synced and displayed reflects the configured meteorological target `LOCATION =`, while the notification displays the player's real-time position on the map!
- **Temporary Weather Cache for Game Session (30 Real-Minute Refresh Cycle)**:
  - Added `SESSION_CACHE = 1` and `CACHE_DURATION_MINUTES = 30` (enabled by default): fetches weather once when the game session starts, and keeps it alive in memory for that single session.
  - Automatically refreshes the cache every 30 real-world minutes of gameplay, completely eliminating the repetitive 300-second polling while keeping weather updated over extended sessions.
  - Ensures clean session-scoped lifecycle: cache is tied to the single active game session (stale caches from prior days/sessions are purged on startup and removed on process exit).
  - Weather persistence is re-affirmed every 15 seconds locally in the game engine without making any internet calls.
  - Pressing `Right-Control + R` (Force Refresh) or editing `RealWeather.ini` immediately flushes the cache and fetches fresh weather.

## [1.0.1] - 2026-09-29

### Fixed
- **Resolved Black Screen on Startup**:
  - Implemented a resilient startup guard in `ScriptMain` that waits until the game world is loaded, player ped exists (`PLAYER::PLAYER_PED_ID`), player is actively playing (`PLAYER::IS_PLAYER_PLAYING`), and the camera has faded in (`CAM::IS_SCREEN_FADED_IN`) before initializing controllers.
  - Replaced unmapped GTA V native hash `0x2CD80B58DA3D396C` with verified RDR2 native hash `0x3A52C59FFB2DEED8` for `CLOCK::SET_CLOCK_TIME`.
  - Replaced unmapped GTA V native hash `0x157F93B036700462` with verified RDR2 native hash `0x1B82FD5FFA4D666E` for `HUD::IS_RADAR_HIDDEN`.
  - Removed unmapped GTA V `HUD::DISPLAY_RADAR` calls that caused runtime memory faults.
- **Thread Safety & ScriptHook Architecture**:
  - Refactored `OnKeyboardMessage` to set atomic request flags (`std::atomic<bool>`) instead of invoking native game functions on the Windows window message thread.
  - Safe lifecycle shutdown in `DllMain`: removed native calls during `DLL_PROCESS_DETACH`.
- **Weather Persistence & UI Rendering**:
  - `ResetWeather` now invokes `MISC::CLEAR_WEATHER_TYPE_PERSIST` (`0xD85DFE5C131E4AE9`) and passes `false` to `SET_CURR_WEATHER_STATE`, returning weather control to the game engine without permanent locking.
  - Formatted subtitle strings via `MISC::VAR_STRING` (`0xFA925AC00EB830B9`) with `10, "LITERAL_STRING"`, ensuring custom subtitles render on-screen.
- **Clock & Timezone Robustness**:
  - Fixed `CLOCK::PAUSE_CLOCK` signature to pass both required arguments (`toggle, 0ULL`).
  - Added host machine system timezone detection fallback if timezone cannot be determined from location heuristics or network.
- **Network Validation & Usability**:
  - Validated HTTP 200 OK status codes and checked for required JSON fields before confirming weather success.
  - Fixed regex hyphen character class ambiguities.
  - Added friendly key-name support in `ParseKey` (`F1`-`F12`, `Ctrl`, `Shift`, `Alt`, letters, digits).
  - Added immediate force-refresh hotkey: `Right-Ctrl + R` (`REFRESH_KEY = 0x52`).

## [1.0.0] - 2026-09-29

### Initial Release (Native ASI for ScriptHook RDR2)
- **Native C++ Implementation**:
  - Full native `.asi` plugin architecture linking directly against `ScriptHookRDR2.lib`.
  - Zero dependencies on .NET or CLR runtimes.
- **Meteorological Engine**:
  - Asynchronous WinHTTP queries running on background worker threads with zero frame hitching.
  - Zero-key Open-Meteo geocoding and live weather synchronization.
  - OpenWeatherMap API key support.
  - Comprehensive mapping of all 22 native RDR2 weather types.
- **Time Controller**:
  - 1:1 real-time clock synchronization using native `CLOCK::PAUSE_CLOCK` and `CLOCK::SET_CLOCK_TIME`.
  - Persistent timezone disk cache (`.tzcache`).
  - Frontier timezone heuristics (Central, Mountain, Pacific, Eastern).
  - 12-hour and 24-hour time formatting.
- **Mission & Cutscene Protection**:
  - 500ms debounced detection of story missions and cinematics via `MISC::GET_MISSION_FLAG`.
- **Western HUD Presentation**:
  - Western subtitle alerts via `UILOG::_UILOG_SET_CACHED_OBJECTIVE` and `_UILOG_PRINT_CACHED_OBJECTIVE`.
  - Radar-aware fallback and peeking support.
  - Frontend audio playback.
- **Runtime Hot-Reloading & Diagnostics**:
  - Real-time `RealWeather.ini` modification tracking and hot-reloading.
  - Thread-safe diagnostic logging to `RealWeather.log` with automatic 2 MB log rotation.
  - Configurable hotkeys (Right-Ctrl + W / T / O).
