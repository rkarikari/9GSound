<div align="center">

# 🎵 9GSound

**Identify what's playing. Log what's on air. Take requests from your listeners.**

A radio-minded music companion for Android — Shazam-style song ID, unattended station monitoring,
synced lyrics, AI music chat, group and private chat, and a serverless song-request mesh.

`Version 1.11` · `Android 8.0+` · `Kotlin` · `Jetpack Compose` · `Material 3`

</div>

---

## ✨ At a Glance

| Tab | What it does |
|---|---|
| 🎤 **Song ID** | Record 8 seconds, get title, artist, album, genre, year, cover art, BPM, key and streaming links |
| 🎵 **Lyrics** | Time-synced lyrics that scroll from the point in the song where you identified it |
| 📻 **Auto** | Hands-free monitoring of a radio station on a timer, even with the screen off |
| 📚 **History** | One entry per song with play counts, charts, and CSV/JSON export & import |
| 🧠 **AI Chat** | Ask about your music taste and get song recommendations from free or local AI models — or switch to **Group** to chat with everyone on the mesh |
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

### 👥 Group chat

A switch at the top of the **AI Chat** tab flips between the AI assistant and a **group chat with everyone on the mesh** — listeners and stations alike.

- **Join** from the tab with a name (the same one used for song requests), or just join the mesh from the Requests tab; the chat is open as soon as you're on it.
- Messages pass **phone to phone** over the same links as song requests, so people several hops apart still reach each other.
- A slim status line shows who you are, how many devices you're linked to and how many people have written in the last 15 minutes. With nobody linked it says so and offers a shortcut to the Requests tab; a line sent into the void is marked *no one was linked* instead of looking delivered.
- Names get a steady colour, same-sender lines are grouped, days are separated, and the view stays put if you've scrolled up to read.
- **Long-press a message** to copy it, **mute** its sender on this device, or — on a station — **ban** them (the same ban the request queue uses).
- A badge on the **AI Chat** tab and on the **Group** side counts unread messages while the chat isn't in front of you.
- The conversation is saved on the device (the last 300 messages) and survives restarts; ⋮ → *Clear chat on this device* empties your copy only.
- Flood limits keep one noisy device from drowning the mesh. Because chat rides the mesh, it only runs while the app is open (or a station is on the air) — there are no background notifications.

### 🔒 Private messages

The switch has a third side, **Private**, for talking to **one person** on the mesh.

- **Everyone who's around is listed** — phones announce themselves on the mesh about once a minute, so people appear (with a green dot while they're online). You can also **long-press a message in the group chat → *Message … privately***.
- Messages are **end-to-end encrypted**: only you and the other person can read them. The phones that pass a message along only see scrambled data.
- **Delivery and read receipts.** A small colour-coded symbol beside each of your messages shows where it has got to: ◷ orange *waiting* (no link yet — it goes out as soon as one appears), ✓ grey *sent*, ✓ blue *delivered* (their phone has it), ✓ green *read* (they had the conversation open). If nothing comes back it is sent again automatically for up to a day, then shows a red **!** — tap it to try again.
- **Read receipts are optional** — turn them off in the ⋮ menu and nobody is told when you've read their messages (you still see theirs). Delivery receipts are always on.
- Two people with the same name show a short code to tell them apart. **Verify security code** (in the conversation's ⋮ menu) lets you compare codes in person to be sure a name belongs to who you think.
- ⋮ also lets you **delete a conversation**, **block** someone (their messages are dropped without telling them), or delete everything. Conversations are saved on the device, encrypted, and survive restarts.
- An unread badge appears on the **AI Chat** tab and on the **Private** side.

## 📡 Requests & Top Songs

A song-request network that runs over plain **TCP/UDP sockets** on Wi-Fi, mobile data, or a VPN overlay such as **ZeroTier** — no server, no Bluetooth, no location permission.

**Roles**
- **Station** — collects requests and Top submissions. Reserved for authorised operators.
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

**Running a station**
- **Station mode is protected.** Only authorised operators can start a station; an ordinary listener's device can't switch it on.
- **Registration persists.** Once started, the station is remembered across app restarts, process kills, phone reboots and in-app updates, and comes back on the air by itself. It is forgotten only when you press **Stop** (which asks to confirm); starting it again then needs authorisation.
- **Keeps running unattended.** A running station is held by its own foreground service (`StationService`) with a CPU wake lock and a Wi-Fi lock, so screen-off, background and swipe-from-recents don't stop it. If Android kills it, the system restarts it and it re-reads its registration. A 30-second watchdog restarts the station if it is ever found stopped, refreshes its shown addresses and ages old requests out of the queue even with the app closed; the mesh transport re-opens its port and discovery sockets by itself if they fail. The notification has no Stop button on purpose — stopping is done in the app.
- **Battery setting.** Stations show a prompt to allow unrestricted battery use; some phone makers (Xiaomi, Huawei, Oppo, Samsung "sleeping apps") also have their own auto-start / background limits that only the person can lift in system settings.
- A station's request queue, ban list and blacklist are held in memory, so they start empty again after a restart; received Top lists are saved as before.

**Station tools**
- Live incoming request queue with shout-outs/dedications.
- Long-press a request to mark it played; it stays hidden for a configurable time (1 h, 3 h, 6 h, 1 day).
- **Spam control:** requests flooding in from one requester are dropped and repeat offenders are automatically banned. Manual **Ban / Unban** is available.
- If no station is connected, requests can still go out through the **system share sheet** (with album art) to any messaging app.

**🏆 Top Songs**
- Listeners send their most-identified songs for the **week, month or year** (Top 10 / 25 / 40 / 50 / 100) from the trophy button.
- The station merges all submissions into a chart ranked by how many listeners share each song.
- The live chart on the Requests tab shows the **current** period only: a list leaves it when its period ends and never carries into the next one. Nothing is lost, though — every list the station receives is also filed in the permanent archive (see **Charts**).
- Periods follow the chart calendar (below).

## 🏆 Charts

A dedicated tab with the **Top 10 / 25 / 40 / 50 / 100** for any **week, month or year** ever recorded.

**The chart calendar.** Every year has 12 months and every month has exactly **4 weeks**, so a year has **48 numbered weeks**: Jan = weeks 1–4, Feb = 5–8, Mar = 9–12 … Dec = 45–48. Week 1 is days 1–7, week 2 is 8–14, week 3 is 15–21 and week 4 is day 22 to the end of the month (so the 29th–31st fall into week 4 and every day belongs to exactly one week).

**Two charts per period — shown automatically by mode**
- **Station chart** — shown while this device is running a station: what the station consolidates from every listener's submission, ranked by how many distinct listeners share each song (total plays break ties).
- **This device** — shown on every other device: this phone's own most-identified songs, built from the dated play log.

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
- **Install.** The system download manager fetches the APK (it survives the app closing). The file is verified before it is handed to the system installer, and Android itself still refuses anything not signed with 9GSound's own key. The first time, Android asks you to allow 9GSound to install apps.
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
| **About** | Version and credit |

The Auto Mode and Requests tabs show their current values with a shortcut that opens this page, and the AI tab's gear opens it too.

## 🌙 Appearance

Material 3 theming with a dark/light toggle (Settings → Appearance). Dark is the default.

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
The single source of truth is at the top of `app/build.gradle.kts` (`appVersionCode`, `appVersionName`). Release APKs are named `9GSound_v<version>_<buildType>.apk`. To ship an update, publish a GitHub release on `rkarikari/9GSound` (marked **Latest**) with that `…_release.apk` attached and a higher `appVersionName`; installed copies will offer it.

---

## ⚠️ Good to Know

- Recognition uses Shazam's **unofficial** endpoint (the same one the `shazamio` library uses), so it may change or be rate-limited without notice.
- Cleartext HTTP is allowed so Ollama can be reached on a local network address.
- Background monitoring uses battery by design; the wake lock is released as soon as Auto Mode stops.

---

## 📜 License

Copyright © R.N.K 9G5AR, RadioSport. All rights reserved.
