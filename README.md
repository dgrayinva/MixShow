# AMP — Live DJ Stream & Interactive Video Fan Wall

**AMP** is a lightweight, zero-cost split-screen web application hosted on GitHub Pages that pairs a live **Twitch** DJ broadcast with a **20-person, video-only WebRTC fan wall** powered by **LiveKit** and **Cloudflare Workers**.

Built specifically for multi-DJ showcases, AMP eliminates Zoom subscriptions and 40-minute timeouts by bringing guest DJ cameras (720p HD) and the audience dancefloor (180p bandwidth-saver mode) into a single browser-based hub—with microphone audio strictly disabled across all video tiles so the DJ set stays front and center.

---

## Live Show URLs & Role Modes

Use the query parameters below depending on your role during the broadcast:

| Role | URL | Features & Behavior |
| :--- | :--- | :--- |
| **Audience / Fans** | `https://github.io` | Watch Twitch on the left, join the video wall on the right (180p, muted), send floating emojis, spotlight a random fan, or toggle **Hide Crowd** to save mobile data. Capped at 20 active cameras. |
| **Guest DJs** | `https://github.io?dj=true` | Publishes camera in **720p HD** (30 fps) with a purple border badge. Pair with **Cleanfeed** in a separate browser tab for lossless stereo audio. |
| **Show Host** | `https://github.io?host=true` | Unlocks **✕ Kick** moderation buttons on every video tile to remove inactive or inappropriate cameras immediately. |
| **OBS — Guest DJs** | `https://github.io?obs=dj` | Borderless, UI-free capture of **only Guest DJ cameras** for clean OBS Browser Source compositing. |
| **OBS — Fan Wall** | `https://github.io?obs=true` | Borderless, UI-free capture of the **entire live video wall** for OBS crowd overlays. |

---

## Architecture & Audio Security

1. **Main Broadcast (Left Pane):** Embeds the host's Twitch channel (`player.twitch.tv`) using split-string URL assembly to prevent browser extensions from stripping `parent` parameters.
2. **Interactive Fan Wall (Right Pane):** Connects via LiveKit WebRTC using lazy-loaded client scripts for fast mobile startup.
3. **3-Layer Audio Block:**
   * Browser microphone permissions are never requested (`setMicrophoneEnabled(false)`).
   * Incoming audio tracks are ignored by the client room listener.
   * Cloudflare Worker JWT tokens enforce `canPublishSources: ["camera"]` at the server level.
4. **4-Hour Monthly Show Optimization:**
   * Hard cap at **20 concurrent cameras** (`~4,800` connection minutes per 4-hour show, staying under LiveKit Cloud's 5,000-minute free monthly limit).
   * Fan cameras run at `320×180` (`15 fps`) with `adaptiveStream` enabled to keep downstream bandwidth within LiveKit's 50 GB monthly allowance.

---

## Setup & Configuration

### 1. LiveKit Cloud
1. Create a free project at [cloud.livekit.io](https://livekit.io).
2. Copy your **WebSocket URL** (`wss://...livekit.cloud`), **API Key**, and **API Secret**.

### 2. Cloudflare Worker (Token Bouncer)
1. Deploy the token generator script to a free Cloudflare Worker at [://cloudflare.com](https://://cloudflare.com).
2. Under **Settings > Variables and Secrets**, add:
   * `LIVEKIT_API_KEY`
   * `LIVEKIT_API_SECRET`
   * `LIVEKIT_HOST` (your `wss://...livekit.cloud` URL)

### 3. Update `index.html`
In `index.html`, update these three values to match your show:
* `var p3 = "?channel=HOSTS_TWITCH_CHANNEL";` — Set to the host's Twitch username.
* `var LIVEKIT_URL = "wss://your-project.livekit.cloud";` — Set to your LiveKit WebSocket URL.
* `var TOKEN_ENDPOINT = "https://workers.dev";` — Set to your Cloudflare Worker URL.

---

## OBS Studio Quick Guide (For the Host)

1. Add a **Browser Source** in OBS.
2. Set the URL to `https://github.io?obs=dj` (for guest DJ cameras) or `https://github.io?obs=true` (for the full fan wall).
3. Set Width to `1920` and Height to `1080` (or match your scene canvas).
4. Check **Shutdown source when not visible** to save bandwidth when switching away from crowd scenes.
