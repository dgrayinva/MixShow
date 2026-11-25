# MIXWALL — Feel The Beat. See The Crowd.

**MIXWALL** is a lightweight, browser-based split-screen web application hosted on GitHub Pages that pairs a live **Twitch** DJ broadcast (`ampdjs`) with a **20-person PA speaker-stack WebRTC fan wall**, **full-screen club lighting & crowd reactions**, and **no-login MIXWALL Chat** powered by **LiveKit** and **Cloudflare Workers**.

Built specifically for multi-DJ showcases, MIXWALL eliminates Zoom subscriptions and 40-minute timeouts by bringing guest DJ cameras (720p HD) and the audience dancefloor (180p bandwidth-saver mode) into a virtual speaker rig—with microphone audio strictly blocked across all video tiles so the DJ set stays front and center.

---

## Live Show URLs & Role Modes

Use the query parameters below depending on your role during the broadcast:

| Role | URL | Features & Behavior |
| :--- | :--- | :--- |
| **Audience / Fans** | `https://github.io` | Watch the host Twitch stream (`ampdjs`) on the left, join the PA speaker stack (180p, muted), chat without a Twitch login, trigger full-screen crowd/club FX, spotlight a random fan, or toggle **Hide Crowd** to save mobile data. Capped at 20 active cameras. |
| **Guest DJs** | `https://github.io?dj=true` | Publishes camera in **720p HD** (30 fps) with a glowing purple speaker cabinet border, cyan chat highlight, and a private top-left dropdown to switch between `ampdjs` (live) and `djtripinva` (testing). Pair with **Cleanfeed** in a separate browser tab for lossless stereo audio. |
| **Show Host** | `https://github.io?host=true` | Includes all DJ features plus **✕ Kick & Block** moderation buttons on every speaker tile to immediately remove and ban disruptive cameras by username and IP. |
| **OBS — Guest DJs** | `https://github.io?obs=dj` | Borderless, UI-free capture of **only Guest DJ cameras** for clean OBS Browser Source compositing. |
| **OBS — Fan Wall** | `https://github.io?obs=true` | Borderless, UI-free capture of the **entire live speaker stack** (including floating emojis, bursts, and club lighting effects) for OBS crowd overlays. |

---

## Key Features

* **PA Speaker-Stack Video Grid:** Styles each connected camera as a modular road-case PA cabinet with corner hex screws, a circular woofer-cone video frame, and a stamped monospace nameplate badge.
* **3-Layer Audio Block:** Browser microphone permissions are never requested (`setMicrophoneEnabled(false)`), incoming audio tracks are ignored, and Cloudflare Worker JWT tokens enforce `canPublishSources: ["camera"]` at the server level.
* **Full-Screen Club Lighting & Crowd Reactions:**
  * **Heart Balloon Pop (`❤️`):** Inflates and shakes a giant heart in the center of the screen before bursting into 36 mixed-size hearts across everyone's screen.
  * **Club Effects:** Full-screen **Strobe Light (`⚡`)** and **4-Corner Inward Fog (`🌫️`)** animations synced across all connected viewers and OBS.
  * **Screen Bursts & Reactions:** 34-piece **Fire Explosion (`🔥`)** with orange stage glow, **Confetti (`🎉`)**, **Fireworks (`🎆`)**, plus floating **Hands Up (`🙌`)** and **Disco Ball (`🪩`)** reactions.
* **No-Login MIXWALL Chat:** Collapsible right-side chat drawer (switches to full-height mobile view on phones) with unread message counters and cyan name highlights for DJs and Hosts.
* **Private Twitch Channel Switcher:** Hidden from fans; appears in the top-left corner of the broadcast box in `?dj=true` or `?host=true` mode to toggle between the live host channel (`ampdjs`) and testing channel (`djtripinva`).
* **Kick & Block Moderation:** Host mode adds a one-click **✕ Kick & Block** button that disconnects the user from LiveKit and blocks their username and IP for the rest of the session.
* **4-Hour Monthly Show Optimization:** Hard cap at **20 concurrent cameras** (`~4,800` connection minutes per 4-hour show, staying under LiveKit Cloud's 5,000-minute free monthly cap) and `320×180` (`15 fps`) fan streams to stay within the 50 GB monthly bandwidth allowance.

---

## Setup & Configuration

### 1. LiveKit Cloud
1. Create a free project at [cloud.livekit.io](https://livekit.io).
2. Under **Settings > Keys**, generate an API key pair and copy your **WebSocket URL** (`wss://...livekit.cloud`), **API Key**, and **API Secret**.

### 2. Cloudflare Worker (Token Bouncer & Moderation)
1. Deploy the Worker script to a free Cloudflare Worker at [://cloudflare.com](https://://cloudflare.com).
2. Under **Settings > Variables and Secrets**, add:
   * `LIVEKIT_API_KEY`
   * `LIVEKIT_API_SECRET`
   * `LIVEKIT_HOST` (your `wss://...livekit.cloud` URL)

### 3. Update `index.html`
In `index.html`, verify these values match your show:
* `HOST_TWITCH` / `TEST_TWITCH` — Set to the host's Twitch channel (`ampdjs`) and test channel (`djtripinva`).
* `LIVEKIT_URL` — Set to your LiveKit project subdomain (`amp-py17bq2u`).
* `TOKEN_ENDPOINT` — Set to your Cloudflare Worker subdomain.

---

## Day-of-Show Pre-Flight Checklist

1. **Audio Routing (DJs & Host):** Guest DJs connect to the Host via **Cleanfeed** in a dedicated browser tab for stereo audio.
2. **OBS Video Sources (Host):**
   * Add a **Browser Source** pointing to `https://github.io?obs=dj` for Guest DJ cameras.
   * Add a **Browser Source** pointing to `https://github.io?obs=true` for the speaker-stack crowd overlay (enable *Shutdown source when not visible* to save bandwidth).
3. **Host Control Monitor:** Open `https://github.io?host=true` on a secondary monitor to manage chat, spotlight fans, and moderate cameras.
4. **Post-Show Wrap-Up:** Ensure all DJs and OBS Browser Sources disconnect (`Leave`) when the stream ends so background WebRTC minutes do not continue counting toward the monthly quota.

---

&copy; 2026 MIXWALL. All Rights Reserved.
