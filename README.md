<div align="center">

# 🎵 9GSound

**Identify what's playing. Log what's on air. Take requests from your listeners.**

A radio-minded music companion for Android — Shazam-style song ID, unattended station monitoring,
synced lyrics, AI music chat, and a serverless song-request mesh.

`Version 1.04` · `Android 8.0+` · `Kotlin` · `Jetpack Compose` · `Material 3`

</div>

---

## ✨ At a Glance

| Tab | What it does |
|---|---|
| 🎤 **Song ID** | Record 8 seconds, get title, artist, album, genre, year, cover art, BPM, key and streaming links |
| 🎵 **Lyrics** | Time-synced lyrics that scroll from the point in the song where you identified it |
| 📻 **Auto** | Hands-free monitoring of a radio station on a timer, even with the screen off |
| 📚 **History** | One entry per song with play counts, charts, and CSV/JSON export & import |
| 🧠 **AI Chat** | Ask about your music taste and get song recommendations from free or local AI models |
| 📡 **Requests** | Device-to-device song requests and weekly/monthly/yearly Top lists — no server needed |
| 🏆 **Charts** | Permanent Top 10 / 25 / 40 / 50 / 100 for every week, month and year, kept for good |

---

## 🎤 Song ID

- Captures **8 seconds** of audio (44.1 kHz, 16-bit mono — falls back to lower rates if the device needs it).
- Generates an audio fingerprint on-device and matches it against Shazam's recognition endpoint.
- Shows cover art, album, genre, release year, BPM, musical key, and links to **Shazam, Spotify, Apple Music and YouTube** when available.
- A live waveform reflects the real microphone level while recording.

## 🎵 Lyrics

- Fetches lyrics from [LRCLIB](https://lrclib.net) (free, no account).
- Follows whichever song was **most recently identified anywhere in the app** — manual, Auto Mode, or background.
- Uses Shazam's match position so a song identified mid-play starts scrolling **near where it actually is**, not from 0:00.
- Falls back to plain-text lyrics when synced lyrics aren't available, and recognises instrumentals.
- No audio is played by the app; scrolling follows a clock, so it works alongside any external player or radio.

## 📻 Auto Mode

Built for station operators and anyone who wants a playlist log without touching the phone.

- Configurable **station name** and **check interval (60 s – 600 s)**.
- Live status, countdown to the next check, songs-identified and error counters, and uptime.
- **Keeps running in the background**: while active, a microphone foreground service and a partial wake lock keep monitoring alive with the screen off. The notification mirrors the live status.
- **Smarter listening**
  - Dead air / silent clips are detected and skipped without contacting the recognition service.
  - If an 8 s clip isn't recognised, one longer **12 s** clip is tried before it counts as an error.
  - Repeats of the same song back-to-back aren't logged twice.
- On Android 13+ the app asks once, via the standard system pop-up, for permission to show the monitoring notification. Monitoring works whether or not you allow it.

## 📚 History

- **One entry per song.** Re-identifying a song raises its play count (×N) and moves its date/time to the latest hit; missing details are filled in from newer results.
- Every play is also kept in a dated log, which powers accurate weekly and monthly charts.
- Thumbnails, station name, a side detail panel on tablets, and a "Send Song Request" dialog.
- **Charts:** library overview, genre distribution, top artists.
- **Export** to your Downloads folder as **CSV** (Excel-friendly) or **JSON**.
- **Import** CSV/JSON back in. Songs are matched case-insensitively, existing ones are left untouched, and missing artwork/album/genre/year are filled from the free iTunes Search API.
- Delete single songs or clear everything (with confirmation).

## 🧠 AI Chat

Chat with an assistant that already knows your listening history.

- **Quick-question chips** return 5, 10 or 20 personalised recommendations as a clean list.
- **Streaming replies** (Server-Sent Events) with your history injected as context.
- **"Ask about these lyrics"** — include the current song's lyrics in the conversation to discuss meaning, themes and wordplay.
- Three providers:

| Provider | Cost | Setup |
|---|---|---|
| ⚡ **LLM7** *(default)* | Free | None. Optionally paste a free token from [token.llm7.io](https://token.llm7.io) for higher limits |
| ☁️ **OpenRouter** | Free models available | Paste your API key |
| 🖥 **Ollama** (local) | Free | Run Ollama on your PC and enter `http://<PC-IP>:11434` |

- **Only working models are listed.** Each LLM7 / OpenRouter model gets a tiny test call the first time the AI tab loads; models that error, return nothing or time out are hidden. Results are saved on the device, are reused across restarts and provider switches, and are discarded only if you tap **Refresh** or change the key/token. Rate limits pause the check rather than wrongly hiding models.

## 📡 Requests & Top Songs

A song-request network that runs over plain **TCP/UDP sockets** on Wi-Fi, mobile data, or a VPN overlay such as **ZeroTier** — no server, no Bluetooth, no location permission.

**Roles**
- **Station** — collects requests and Top submissions.
- **Listener** — sends requests and Top lists to the station.

**How it connects**
1. **UDP broadcast discovery** on the local network.
2. **Automatic subnet sweep** — finds peers across VPN overlays where broadcast can't reach.
3. **Manual connect** by IP address as a last resort.

Requests **flood hop by hop** (up to 6 hops), so listeners don't need to be near the station. Ports: TCP `7654`, UDP `7655`.

**Station tools**
- Live incoming request queue with shout-outs/dedications.
- Long-press a request to mark it played; it stays hidden for a configurable time (1 h, 3 h, 6 h, 1 day).
- **Spam control:** more than 6 requests in 30 s from one requester are dropped; repeated offenders are automatically banned. Manual **Ban / Unban** is available.
- If no station is connected, requests can still go out through the **system share sheet** (with album art) to any messaging app.

**🏆 Top Songs**
- Listeners send their most-identified songs for the **week, month or year** (Top 10 / 25 / 40 / 50 / 100) from the trophy button.
- The station merges all submissions into a chart ranked by how many listeners share each song.
- The live chart on the Requests tab shows the **current** period only: a list leaves it when its period ends and never carries into the next one. Nothing is lost, though — every list the station receives is also filed in the permanent archive (see **Charts**).
- Periods follow the chart calendar (below).

## 🏆 Charts

A dedicated tab with the **Top 10 / 25 / 40 / 50 / 100** for any **week, month or year** ever recorded.

**The chart calendar.** Every year has 12 months and every month has exactly **4 weeks**, so a year has **48 numbered weeks**: Jan = weeks 1–4, Feb = 5–8, Mar = 9–12 … Dec = 45–48. Week 1 is days 1–7, week 2 is 8–14, week 3 is 15–21 and week 4 is day 22 to the end of the month (so the 29th–31st fall into week 4 and every day belongs to exactly one week).

**Two charts per period**
- **Station chart** — what the station consolidates from every listener's submission, ranked by how many distinct listeners share each song (total plays break ties).
- **This device** — this phone's own most-identified songs, built from the dated play log.

**Permanent by design**
- Stored in its own Room database (`9gsound_charts.db`), separate from song history. Nothing in it is ever expired or pruned, it survives reboots and app updates, and clearing History, importing songs or the Requests tab's **Clear** button never touches it. There is deliberately no destructive-migration fallback.
- Every submission is written to disk the moment the station receives it — including a list that arrives *after* its period ended (it is filed under the period it was made for) and lists that were saved while the app was closed. A listener's newer list replaces its older one; an older one that arrives late is ignored.
- Each period's ranking is stored down to **rank 100**, so Top 10, 25, 40, 50 and 100 are always all on record (each is the first N rows of the same ranking).
- The device chart is re-derived from the play log whenever history changes, starting with the app process itself (so background Auto Mode is covered), and a period is never erased if its plays later disappear.
- **Backup / CSV / Restore** buttons export the whole archive to **Downloads** (Android 10+; the app's Documents folder on older versions) and merge a backup back in — so years of charts also survive uninstalling the app or changing phones. Restoring only ever adds or updates; it never deletes.
- Android's normal app backup also covers the database where the device has it enabled.

Pick **Weekly / Monthly / Yearly**, choose the year, month and week (a • marks periods that have a chart), or step with the ‹ › arrows.

## 🌙 Appearance

Material 3 theming with a dark/light toggle. Dark is the default.

---

## 🔒 Privacy & Security

- **API keys are encrypted at rest.** The OpenRouter key and LLM7 token are stored with AES-256-GCM using a key held in the **Android Keystore**. Keys saved by older versions are upgraded automatically on first launch. If a backup is restored onto a different device, the keys can't be decrypted there and simply need to be re-entered.
- **Local by default.** Song history lives in an on-device Room database and is never uploaded by the app.
- **Microphone use is explicit.** Audio is recorded only during identification (manual or Auto Mode), and only a compact fingerprint — not the recording — is sent for matching.
- **Database safety.** A missing migration can no longer silently wipe history; only a downgrade may reset the database. Schema export is enabled for migration testing.

## 🔐 Permissions

| Permission | Why |
|---|---|
| `RECORD_AUDIO` | Listening for songs |
| `INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE` | Recognition, lyrics, AI, and the request mesh |
| `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_MICROPHONE` | Background Auto Mode |
| `WAKE_LOCK` | Keeps Auto Mode timing reliable with the screen off |
| `POST_NOTIFICATIONS` | Shows the Auto Mode notification (Android 13+) |
| `RECEIVE_BOOT_COMPLETED` | Declared for future scheduled monitoring |

No location, Bluetooth, contacts or storage permissions are needed. Exports use `MediaStore`.

---

## 🛠 Build Requirements

| Tool | Version |
|---|---|
| Android Studio | Ladybug / Meerkat / Panda or newer |
| Android Gradle Plugin | 8.7.3 |
| Kotlin | 2.1.0 |
| Compile / Target SDK | 35 |
| Min SDK | 26 (Android 8.0) |
| Java | 17 |

**Stack:** Jetpack Compose · Material 3 · Hilt · Room · Retrofit/OkHttp · Kotlin Coroutines & Flow · DataStore · WorkManager · Coil · Timber

### Getting started
1. **File → Open** and select the `9GSound` folder.
2. Click **Sync Now** when prompted.
3. Run on a **real device** for reliable microphone behaviour.
4. Grant the **Microphone** permission on first use.

### Versioning
The single source of truth is at the top of `app/build.gradle.kts` (`appVersionCode`, `appVersionName`). Release APKs are named `9GSound_v<version>_<buildType>.apk`.

---

## 📁 Project Structure

```
9GSound/
├── build.gradle.kts · settings.gradle.kts · gradle/libs.versions.toml
└── app/src/main/
    ├── AndroidManifest.xml
    ├── res/                       — strings, themes, launcher icons, FileProvider paths
    └── java/com/radiosport/ninegsound/
        ├── MainActivity.kt        — Application class (NineGSoundApp) + main activity
        ├── data/
        │   ├── api/               — Retrofit services: LLM7, OpenRouter, Ollama, LRCLIB
        │   ├── models/            — domain models, Room entities, Shazam & mesh models,
        │   │                        chart calendar (ChartKey) & chart archive entities
        │   └── repository/        — Song, Ai, Lyrics, Settings, Mesh repositories,
        │                            Room database, model-verdict & Top-chart stores,
        │                            permanent chart archive (ChartDatabase, ChartArchiveRepository)
        ├── di/                    — Hilt module (Room, Retrofit, Gson, OkHttp)
        ├── service/               — AutoMonitorService (foreground, microphone)
        ├── ui/
        │   ├── components/        — shared composables
        │   ├── screens/           — SongId, Lyrics, AutoMode, History, AiAnalysis, MeshRequest, Charts, Navigation
        │   └── theme/             — Material 3 colours and typography
        ├── utils/                 — AudioRecorder, ShazamFingerprint, ShazamRecognizer, SongEnricher,
        │                            SongExporter, ChartExporter, SongShareHelper, LrcParser, TopChart,
        │                            MeshNetworkManager, SecretCipher
        └── viewmodel/             — Song, Lyrics, Auto Mode, AI, Settings, Mesh, Charts view models
```

`app/src/test` holds JVM unit tests for the chart calendar and ranking rules.

---

## ⚠️ Good to Know

- Recognition uses Shazam's **unofficial** endpoint (the same one the `shazamio` library uses), so it may change or be rate-limited without notice.
- Cleartext HTTP is allowed so Ollama can be reached on a local network address.
- Background monitoring uses battery by design; the wake lock is released as soon as Auto Mode stops.

---

## 🆕 What's New in 1.04

- New **Charts** tab: Top 10 / 25 / 40 / 50 / 100 for any week, month or year, kept permanently.
- Top lists now cover the **year** as well as the week and month, and **Top 40** joins the list sizes.
- Weeks now follow the chart calendar (4 per month, 48 per year) instead of Monday–Sunday weeks.
- Station keeps every received list forever (even late ones); backup, CSV export and restore of the whole archive.
- Seven tabs now: the navigation labels are slightly smaller.

## 🆕 What's New in 1.03

- Auto Mode now truly runs in the background (foreground service + wake lock).
- A single failed recording no longer stops background monitoring.
- Silence detection and a longer retry clip improve unattended accuracy.
- OpenRouter key and LLM7 token are encrypted with the Android Keystore.
- History database can no longer be silently wiped by a missing migration.
- Notification permission is requested with the standard Android pop-up.

---

## 📜 License

Copyright © R.N.K 9G5AR, RadioSport. All rights reserved.
