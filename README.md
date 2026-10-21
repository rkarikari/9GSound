<div align="center">

# 🎵 9GSound

**Identify what's playing. Log what's on air. Take requests from your listeners.**

A radio-minded music companion for Android — Shazam-style song ID, unattended station monitoring,
synced lyrics, AI music chat, and a serverless song-request mesh.

`Version 1.08` · `Android 8.0+` · `Kotlin` · `Jetpack Compose` · `Material 3`

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
- **Station** — collects requests and Top submissions. Entered only through the hidden security entry (see *Running a station*); the Requests tab has no Station button.
- **Listener** — sends requests and Top lists to the station.

**How it connects**
1. **UDP broadcast discovery** on the local network.
2. **Automatic subnet sweep** — finds peers across VPN overlays where broadcast can't reach.
3. **Manual connect** by IP address as a last resort — accepts `ip` or `ip:port`, so a station across the internet can be dialed directly.
4. **Join links** — a station can share a `ninegsound://join?host=…&port=…` link (or a companion-page link / QR); tapping it asks before connecting.

Requests **flood hop by hop** (up to 6 hops), so listeners don't need to be near the station. Ports: TCP `7654`, UDP `7655`.

**🌍 Reaching a station from outside its network**

A station finds its own way onto the internet, and the Requests tab says plainly whether it worked:

1. **Public address.** Several independent sources are asked at once — four HTTPS echo services and two STUN servers — and the majority wins, so one wrong or slow service can't decide it. Private, carrier-grade (`100.64/10`) and garbled answers never count.
2. **Opening the port.** The router is asked to forward TCP `7654` using **PCP**, then **NAT-PMP**, then **UPnP**. If the router already has the port for another device, a different external port is requested and reported. Mappings are leased for an hour, renewed while the station runs, and removed when it stops.
3. **Honest about what can't work.** If the router's own internet address isn't public (carrier-grade NAT), or the phone is on mobile data, no port can be opened — the card says so and points to alternatives (public IP from the provider, IPv6, or a ZeroTier network) instead of pretending.
4. **Proof from outside (optional).** With the [companion page](companion/README.md) set in Settings, a server on the internet connects back to the station and reports whether a 9GSound station answered. Without it, the card says "port opened" but not "proven".
5. **Staying right.** It re-checks on network changes and every few minutes, because public addresses change.

Hardening for an open port: at most 64 live links (8 per remote address); a link must send a small, valid HELLO within 15 s or it is dropped; nothing larger than 1 KB is accepted before HELLO; a link sending more than 300 frames in 10 s is cut off.

**Running a station**
- **Station mode is hidden and code-protected.** There is no Station button on the Requests tab. Open **Settings → About** and tap the About card **8 times in quick succession** (nothing shows while you tap; the 8th gives a short buzz) to bring up the *Station access* pop-up. Enter the station name *and the code issued for that name* — a wrong code does nothing. When the code is accepted the pop-up closes, the station starts and the app takes you to the Requests tab. If the device is already running a station, the 8 taps just say so. Codes are not chosen by the person at the device: each is derived from the station name with a secret fixed when the app is built (see *Station codes* under Build), so only whoever builds the app can issue one. Case and repeated spaces in the name don't matter. Four wrong tries are free; after that entry locks for 30 s, doubling per further miss up to 15 min, and the count survives app restarts.
- **Registration persists.** Once accepted, the station is remembered across app restarts, process kills, phone reboots and in-app updates, and comes back on the air by itself — without asking for the code again. It is forgotten only when you press **Stop** (which asks to confirm); starting again then needs the code. The code is stored encrypted with the Android Keystore and re-checked against the name every time it is read, so editing stored data or restoring a backup on another phone can't create a working station.
- **Keeps running unattended.** A running station is held by its own foreground service (`StationService`) with a CPU wake lock and a Wi-Fi lock, so screen-off, background and swipe-from-recents don't stop it. If Android kills it, the system restarts it and it re-reads its registration. A 30-second watchdog restarts the station if it is ever found stopped, refreshes its shown addresses and ages old requests out of the queue even with the app closed; the mesh transport re-opens its port and discovery sockets by itself if they fail. The notification has no Stop button on purpose — stopping is done in the app.
- **Battery setting.** Stations show a prompt to allow unrestricted battery use; some phone makers (Xiaomi, Huawei, Oppo, Samsung "sleeping apps") also have their own auto-start / background limits that only the person can lift in system settings.
- A station's request queue, ban list and blacklist are held in memory, so they start empty again after a restart; received Top lists are saved as before.

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

## 🔄 Updates

9GSound can tell you when a newer release is out and install it for you.

- **What it checks.** The repository's *Latest* GitHub release (`rkarikari/9GSound`). Its version comes from the release tag, title or APK file name (e.g. `9GSound_v1.05_release.apk`); it is offered only if it is strictly newer than the installed version.
- **When.** Only while the app is open on screen — no background job, alarm or service. It waits about 45 seconds after the app comes up, looks at most **once a day**, and retries a failed look after an hour. It stands down on Battery Saver, with no connection, or on a metered connection under Data Saver, and tries again the next time you open the app.
- **Light on data.** One small request (a few KB) on its own low-priority thread, conditional (ETag) so an unchanged answer is nearly free; if GitHub's API refuses, it falls back to the `/releases/latest` redirect.
- **The pop-up.** A small translucent card under the top bar. Tap it to download and install; swipe it away to hide that release until a newer one appears. While downloading it shows a progress bar and percentage.
- **Install.** The system download manager fetches the APK (it survives the app closing). The file is then checked — size as GitHub reports it, that it is really 9GSound, that its version code is newer — before it is handed to the system installer. Android itself still refuses anything not signed with 9GSound's own key. The first time, Android asks you to allow 9GSound to install apps.
- **Check now.** Settings → **App updates** → **Check now** looks immediately: it ignores the once-a-day limit and Battery Saver / Data Saver (it still needs a connection), and brings back a pop-up you swiped away if that release is still newer.
- **Always says what happened.** The line under the button reports how the last look ended — *up to date*, *update available*, *could not reach GitHub (reason)*, or *skipped (Battery Saver / Data Saver / offline)* — and when it was. Switching the check on also looks straight away if one is due.
- **Switch it off.** Settings (gear in the top bar) → **App updates** → **Check for new version**.

## ⚙️ Settings

Tap the **gear** in the top bar to open the Settings page — every app setting is in this one place, and each control saves as you change it:

| Section | What it holds |
|---|---|
| **Appearance** | Dark / light theme (dark is the default) |
| **Auto Mode** | Station name and how often it listens |
| **Song Requests** | The name sent with your requests; how long a played request stays hidden from the station queue |
| **Internet access (Station)** | Whether a station opens its port on the router automatically |
| **AI Chat** | Provider (LLM7 / OpenRouter / Ollama), token, API key or host, model and model refresh |
| **App updates** | The *Check for new version* switch and the installed version |
| **About** | Version and credit. Tapping this card 8 times in a row (each tap within 2 s of the last) opens the Station security entry |

The Auto Mode and Requests tabs show their current values with a shortcut that opens this page, and the AI tab's gear opens it too.

## 🌙 Appearance

Material 3 theming with a dark/light toggle (Settings → Appearance). Dark is the default.

---

## 🔒 Privacy & Security

- **API keys are encrypted at rest.** The OpenRouter key and LLM7 token are stored with AES-256-GCM using a key held in the **Android Keystore**. Keys saved by older versions are upgraded automatically on first launch. If a backup is restored onto a different device, the keys can't be decrypted there and simply need to be re-entered.
- **Station internet access.** While a station runs with *Open the port automatically* on, it contacts public address-lookup services (api.ipify.org, checkip.amazonaws.com, icanhazip.com, ifconfig.me, and Google/Cloudflare STUN servers), which see your IP address as any website would. It opens one port on your router, which exposes the station to the internet until it stops. The built-in companion page (https://rkarikari.github.io/9GSound, which names its helper server in its config.json) and that server also see your address and port, and **a reachable station is listed in a public directory of stations on air (its name, public address and port) until it stops**, so listeners can find it. Turn the switch off in Settings to do none of this.
- **Local by default.** Song history lives in an on-device Room database and is never uploaded by the app.
- **Microphone use is explicit.** Audio is recorded only during identification (manual or Auto Mode), and only a compact fingerprint — not the recording — is sent for matching.
- **Database safety.** A missing migration can no longer silently wipe history; only a downgrade may reset the database. Schema export is enabled for migration testing.

## 🔐 Permissions

| Permission | Why |
|---|---|
| `RECORD_AUDIO` | Listening for songs |
| `INTERNET`, `ACCESS_NETWORK_STATE`, `ACCESS_WIFI_STATE` | Recognition, lyrics, AI, and the request mesh |
| `CHANGE_WIFI_MULTICAST_STATE` | Lets a station hear its router's UPnP answer while it opens the mesh port (held for a few seconds at a time) |
| `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_MICROPHONE` | Background Auto Mode |
| `FOREGROUND_SERVICE_SPECIAL_USE` | The station foreground service that keeps a registered station on the air (Android 14+ requires a type-specific permission) |
| `WAKE_LOCK` | Keeps Auto Mode and a running station reliable with the screen off |
| `POST_NOTIFICATIONS` | Shows the Auto Mode and station notifications (Android 13+) |
| `RECEIVE_BOOT_COMPLETED` | Brings a registered station back after a restart or an app update |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Lets you exempt 9GSound from battery optimization so Android doesn't pause a station running for hours |
| `REQUEST_INSTALL_PACKAGES` | Hands a downloaded 9GSound update to the system installer (only after you tap the update pop-up) |

No location, Bluetooth, contacts or storage permissions are needed. Exports use `MediaStore`; update downloads go to the app's own external files folder.

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

### Station codes
Station mode is unlocked by a code derived from the station name with a **build secret** (`HMAC-SHA256`, shortened to `XXXX-XXXX`). Provide the secret from outside the repository — never commit it — by any of:
- `stationCodeSecret=<long random text>` in `~/.gradle/gradle.properties`
- `./gradlew assembleRelease -PstationCodeSecret=<…>`
- the environment variable `NINEG_STATION_SECRET`

A **release build made without a secret has no valid codes**, so Station mode stays locked (it fails closed, and the Requests tab says so). Debug builds fall back to a fixed test secret (`9gsound-debug-only-secret`) so station mode can be tried while developing. Keep using the same secret for every release: changing it invalidates every code already issued, and registered stations stop being recognised.

Issue a code with `tools/station_code.py`. It finds the secret the same way the build does — `stationCodeSecret` in `~/.gradle/gradle.properties` (or `$GRADLE_USER_HOME`), then the project's own `gradle.properties`, then `NINEG_STATION_SECRET` — and only prompts if none is set. It prints where the secret came from, so a mismatch with the build is easy to spot:

```
python3 tools/station_code.py "Joy FM" "Peace FM"
```

The secret is compiled into the app, so this stops casual use and name impersonation, not someone who takes the APK apart and extracts it.

### Versioning
The single source of truth is at the top of `app/build.gradle.kts` (`appVersionCode`, `appVersionName`). Release APKs are named `9GSound_v<version>_<buildType>.apk`. To ship an update, publish a GitHub release on `rkarikari/9GSound` (marked **Latest**) with that `…_release.apk` attached and a higher `appVersionName`; installed copies will offer it. The repository is set by `UpdateLogic.REPO`.

---

## 📁 Project Structure

```
9GSound/
├── build.gradle.kts · settings.gradle.kts · gradle/libs.versions.toml
├── tools/                         — station_code.py (issues station codes)
└── app/src/main/
    ├── AndroidManifest.xml
    ├── res/                       — strings, themes, launcher icons, FileProvider paths
    └── java/com/radiosport/ninegsound/
        ├── MainActivity.kt        — Application class (NineGSoundApp) + main activity
        ├── data/
        │   ├── api/               — Retrofit services: LLM7, OpenRouter, Ollama, LRCLIB
        │   ├── models/            — domain models, Room entities, Shazam & mesh models,
        │   │                        chart calendar (ChartKey) & chart archive entities
        │   └── repository/        — Song, Ai, Lyrics, Settings, Mesh, Station registry repositories,
        │                            Room database, model-verdict & Top-chart stores,
        │                            permanent chart archive (ChartDatabase, ChartArchiveRepository)
        ├── di/                    — Hilt module (Room, Retrofit, Gson, OkHttp)
        ├── reach/                 — station reachability: ReachabilityLogic (pure rules + wire formats),
        │                            PortMapper (PCP / NAT-PMP / UPnP), PublicIpResolver, ReachabilityManager
        ├── service/               — AutoMonitorService (foreground, microphone),
        │                            StationService (foreground, keeps a station on the air), StationBootReceiver
        ├── update/                — in-app updates: UpdateLogic (pure rules), UpdateChecker (GitHub look),
        │                            UpdateInstaller (download, verify, install), UpdatePrompt (pop-up + settings rows)
        ├── ui/
        │   ├── components/        — shared composables
        │   ├── screens/           — SongId, Lyrics, AutoMode, History, AiAnalysis, MeshRequest, Charts, Settings, StationEntryDialog (hidden station security entry), Navigation
        │   └── theme/             — Material 3 colours and typography
        ├── utils/                 — AudioRecorder, ShazamFingerprint, ShazamRecognizer, SongEnricher,
        │                            SongExporter, ChartExporter, SongShareHelper, LrcParser, TopChart,
        │                            MeshNetworkManager, SecretCipher, StationCode, TapSequenceDetector
        └── viewmodel/             — Song, Lyrics, Auto Mode, AI, Settings, Mesh, Charts view models
```

`companion/` (beside `app/`) is the optional helper page and connect-back test for stations — a small Cloudflare Worker; see [companion/README.md](companion/README.md).

`app/src/test` holds JVM unit tests for the chart calendar and ranking rules, the update rules (`UpdateLogicTest`), the reachability rules and wire formats (`ReachabilityLogicTest`), the port-opening code run against fake routers on loopback (`PortMapperTest`), the station code and lock-out rules (`StationCodeTest`), and the 8-tap rule behind the hidden station entry (`TapSequenceDetectorTest`).

---

## ⚠️ Good to Know

- Recognition uses Shazam's **unofficial** endpoint (the same one the `shazamio` library uses), so it may change or be rate-limited without notice.
- Cleartext HTTP is allowed so Ollama can be reached on a local network address.
- Background monitoring uses battery by design; the wake lock is released as soon as Auto Mode stops.

---

## 🆕 What's New in 1.08 (this build)

- **Station entry is hidden.** The Station button is gone from the Requests tab, which is now listener-only. Tap **Settings → About** 8 times in quick succession (each tap within 2 s of the last) to bring up the *Station access* pop-up, which takes the station name and its code; when accepted, the station starts and the app opens the Requests tab. The code check and lock-outs are unchanged. If a station is already running, the 8 taps just say so.
- **`tools/station_code.py` finds the secret itself.** It reads `stationCodeSecret` from `~/.gradle/gradle.properties` (then the project's `gradle.properties`, then `NINEG_STATION_SECRET`) the same way the build does, and only prompts if none is set.
- New unit tests for the 8-tap rule (`TapSequenceDetectorTest`).

## 🆕 What's New in 1.07

- **Station mode needs a code.** Becoming a station now takes the station name *and* the code issued for that name; a wrong code leaves the device a listener, with lock-outs after repeated misses. Codes are issued with `tools/station_code.py` from a build secret (see *Build → Station codes*).
- **Stations survive restarts.** The registration is saved (code encrypted with the Keystore) and the station comes back on its own after an app restart, a kill, a reboot or an update.
- **Stations stay on the air.** New `StationService` foreground service with wake and Wi-Fi locks, watchdog and sticky restart; the mesh port and discovery sockets now re-open themselves instead of giving up on the first error; Stop asks for confirmation; a prompt offers the battery exemption. New permissions `FOREGROUND_SERVICE_SPECIAL_USE` and `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS`; `RECEIVE_BOOT_COMPLETED` is now used.

- **Stations reach the internet by themselves.** A station works out its public address from several sources, asks the router to open the mesh port (PCP, NAT-PMP, then UPnP), keeps the mapping renewed, and shows the result on the Requests tab with a plain-language reason when it can't (carrier-grade NAT, mobile data, router refusing). See *Requests → Reaching a station from outside its network*.
- **Share a station.** *Share link* hands out a join link; tapping one in a message opens 9GSound and asks before connecting. *Connect by IP* now takes `ip:port`.
- **Companion page (optional).** `companion/` holds a small web page and connect-back test that proves the port works from the real internet and builds link/QR sharing.
- **Safer open port.** Connection caps, a handshake deadline, a size limit before HELLO, and a flood ceiling on every link.
- New Settings section **Internet access (Station)**; new permission `CHANGE_WIFI_MULTICAST_STATE`.

## 🆕 Update-check fix and top-bar subtitle (1.05)

- **Update check fixed and made visible.** Settings → App updates now has a **Check now** button and a status line that says how the last check ended (up to date / update available / could not reach GitHub, with the reason / skipped for Battery Saver, Data Saver or no connection). Previously a skipped or failed check was silent, and a check could only happen once a day. Modelled on 9GRadio's update-check report.
- Turning **Check for new version** on now checks straight away; a manual check also re-offers an update you had swiped away.
- The line under the app name in the top bar now reads **Quality control on AirPlay** (the copyright and version stay in the About card at the bottom of Settings).
- A debug build that is offered the release APK now says why it cannot install it instead of "not a newer build".

## 🆕 What's New in 1.05

- New **Settings page** (gear in the top bar) that gathers every app setting in one place: theme, Auto Mode station name and interval, request name and hide-played duration, and the AI provider, key and model. Dark/light is now switchable in the app.
- **In-app updates:** a pop-up offers newer GitHub releases and installs them (see *Updates*); switch it off under Settings → App updates.
- Auto Mode's settings card is now a compact summary with a shortcut to Settings; the Requests tab's duration chip opens Settings.
- Fix: Auto Mode's station name and interval are now actually saved (they were only kept for the open screen).

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
