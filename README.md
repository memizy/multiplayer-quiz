# 🎮 Multiplayer Quiz – OQSE Plugin

A real-time multiplayer quiz game for OQSE study sets. Perfect for classroom competitions and study group challenges.

**Live Demo:** [https://memizy.github.io/multiplayer-quiz/](https://memizy.github.io/multiplayer-quiz/)

Standalone Memizy multiplayer plugin example built around the modern `@memizy/multiplayer-sdk` v0.4 architecture using secure iframe RPC (Penpal).

## 💡 What it demonstrates

  - Initializing the `MemizyMultiplayerSDK` and connecting to the host safely via Penpal.
  - Handling strict role separation (`host` vs. `player`).
  - Utilizing the `host` manager to author and broadcast authoritative game state (`sdk.host.setState`, `sdk.host.updateState`).
  - Utilizing the `player` manager to submit intents (`sdk.player.sendAction`) and mirror state (`sdk.player.onStateChange`).
  - Authoring host-side settings during the `host-settings` phase.
  - Managing the forward-only lifecycle phases: `host-settings` ➔ `synchronizing` ➔ `playing` ➔ `finished`.
  - Manifest data island support for standalone local development and plugin registry discovery.

## 🔄 Runtime flow

1.  Open the plugin in a browser or inside the Memizy host.
2.  The SDK reads the OQSE manifest from the HTML data island.
3.  The plugin executes `await sdk.connect()` to establish a Penpal handshake with the host application.
4.  The host resolves the connection and provides an `InitSessionPayload` (containing items, players, role, and phase).
5.  The Host UI handles settings and calls `sdk.room.startGame()` to transition through the synchronizing and playing phases, while Player UIs report readiness via `sdk.room.clientReady()`.

-----

## ✨ Features

  - **Real-time Multiplayer:** Teachers host a game, students join with a PIN.
  - **Live Leaderboard:** See scores update in real-time as students answer.
  - **Configurable Settings:**
      - ⏱️ Time per question (5–300 seconds)
      - 🔀 Shuffle questions
      - ⚡ Auto-advance after answer reveal
      - 📊 Periodic leaderboard display
  - **Multiple Question Types:** MCQ (single/multi), True/False, Short Answer, Matching.
  - **Rich Content Support:** Markdown and images in questions (rendered securely via `sdk.text`).
  - **Mobile-Friendly:** Touch-optimized for tablets and smartphones.
  - **Offline-First:** Runs entirely in the browser.

-----

## 📋 OQSE Compatibility

This plugin implements the **OQSE (Open Quiz & Study Exchange) v0.1** specification.

| Property | Value |
|----------|-------|
| **Manifest Version** | 0.1 |
| **Plugin Version** | 1.0.0 |
| **Min OQSE Version** | 0.1 |
| **Max OQSE Version** | 1.99 |

### Supported Item Types

  - `mcq-single` – Multiple choice (one answer)
  - `mcq-multi` – Multiple choice (multiple answers)
  - `true-false` – True/False statements
  - `short-answer` – Free text responses
  - `match-pairs` – Matching pairs

### Capabilities

  - **Actions:** `render` (study/game mode)
  - **Assets:** Images (JPEG, PNG, SVG, WebP)
  - **Features:** Markdown

-----

## 🚀 Deployment

### Option 1: GitHub Pages (Recommended)

1.  **Create a new repository** on GitHub:

    ```
    https://github.com/your-org/plugin-multiplayer-quiz
    ```

2.  **Add the plugin files:**

    ```
    plugin-multiplayer-quiz/
    ├── index.html          # Main plugin file (HTML + inline JS/CSS)
    ├── README.md           # This file
    └── LICENSE             # MIT or your chosen license
    ```

3.  **Enable GitHub Pages:**

      - Go to Settings → Pages
      - Set "Source" to `main` branch
      - Your plugin will be available at:
        ```
        https://your-org.github.io/plugin-multiplayer-quiz/
        ```

4.  **Register in OQSE Plugin Registry** (optional):

      - Submit to the Memizy Plugin Registry to make it discoverable.

### Option 2: Cloudflare Pages

1.  Connect your GitHub repo to Cloudflare Pages.
2.  Plugin will be deployed at:
    ```
    https://plugin-multiplayer-quiz.pages.dev/
    ```

### Option 3: Self-Hosted

Serve the `index.html` file on any HTTP server:

```bash
python -m http.server 8080
# Visit: http://localhost:8080/index.html
```

-----

## 🔌 Integration (v0.4 SDK)

Unlike older v0.2/v0.3 plugins that used raw `window.postMessage`, this plugin leverages the `@memizy/multiplayer-sdk` wrapper for strict typing, automatic state patching, and secure iframe RPC.

```javascript
import { MemizyMultiplayerSDK } from 'https://esm.sh/@memizy/multiplayer-sdk@0.4.0';

// 1. Initialize the SDK
const sdk = new MemizyMultiplayerSDK({
  id: 'com.example.multiplayer-quiz',
  version: '1.0.0'
});

// 2. Register lifecycle callbacks
sdk.onInit(async (init) => {
  if (init.role === 'host') {
    // Setup Host UI and settings
    await sdk.settings.setValid(true);
  } else if (init.role === 'player') {
    // Setup Player UI
    await sdk.room.clientReady();
  }
});

sdk.onPlayerAction(async (playerId, action) => {
  // Host handles answers
  if (action.type === 'answer') {
    // Update authoritative state
  }
});

// 3. Connect to the host application
await sdk.connect();
```

-----

## ⚙️ Multiplayer Declaration (Manifest)

The plugin declares multiplayer contract metadata in a `<script type="application/oqse-manifest+json">` data island inside `index.html`:

```json
{
  "appSpecific": {
    "memizy": {
      "multiplayer": {
        "apiVersion": "0.4",
        "players": {
          "min": 2,
          "max": 60,
          "recommended": 30
        },
        "supportsLateJoin": true,
        "supportsReconnect": true,
        "supportsTeams": false,
        "requiresHostScreen": true,
        "clientOrientation": "portrait"
      }
    }
  }
}
```

Host applications can:

1.  Read which multiplayer API contract version the plugin targets (e.g., `0.4`).
2.  Enforce or guide lobby size via the declared players range.
3.  Apply lobby/runtime behavior such as late-join, reconnect, team UI, and client orientation hints.

-----

## 📱 Mobile & Accessibility

  - ✅ Touch-friendly buttons (44×44 px min)
  - ✅ Responsive design (mobile, tablet, desktop)
  - ✅ Semantic HTML (`<nav>`, `<main>`, `<article>`)
  - ✅ High color contrast (WCAG AA)
  - ✅ Keyboard navigation (Tab, Enter, Escape)

-----

## 🛠️ Development

### Local Testing

1.  Serve the file locally:

    ```bash
    python -m http.server 8080
    ```

2.  Open in browser to trigger **Standalone Mock Mode**:

    ```
    http://localhost:8080/index.html
    ```

    *Note: When opened outside of a Memizy iframe, the SDK automatically injects a developer landing page with buttons to launch local "Host" or "Player" mock instances\!*

### Manifest Validation

Verify your manifest is valid JSON directly in the browser console:

```javascript
console.log(JSON.parse(document.querySelector('script[type="application/oqse-manifest+json"]').textContent));
```

-----

## 📄 License

[MIT License](https://www.google.com/search?q=LICENSE) – Feel free to fork, modify, and redistribute.

-----

## 🤝 Contributing

Issues and pull requests welcome\! Please:

1.  Test on mobile + desktop.
2.  Verify OQSE manifest validity.
3.  Update this README if adding features.
4.  Follow the [OQSE Specification](https://github.com/memizy/oqse).

-----

## 📚 Resources

  - **OQSE Spec:** [https://github.com/memizy/oqse](https://github.com/memizy/oqse)
  - **Memizy:** [https://memizy.com](https://memizy.com)
  - **Plugin SDK:** [https://github.com/memizy/multiplayer-sdk](https://www.google.com/search?q=https://github.com/memizy/multiplayer-sdk)
  - **Manifest Format:** OQSE v1.0 Application Manifest