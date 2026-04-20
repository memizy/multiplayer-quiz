# Multiplayer Quiz Plugin

Standalone Memizy multiplayer plugin example built around the `@memizy/multiplayer-sdk` v0.3 Split-Lobby lifecycle.

## What it demonstrates

- `PLUGIN_READY` emitted by the plugin as soon as it boots.
- `INIT_SESSION` received from the host with session data.
- `PREPARE_GAME` and `START_GAME` lifecycle events (Split-Lobby 3-phase start).
- `MULTI_READY` support for plugin-side readiness signaling.
- Manifest data island support for standalone mode and plugin discovery.

## Runtime flow

1. Open the plugin in a browser or inside the Memizy host.
2. The SDK reads the OQSE manifest from the HTML data island.
3. The plugin announces `PLUGIN_READY`.
4. The host responds with `INIT_SESSION`.
5. The host can drive `PREPARE_GAME` and `START_GAME`, and the plugin broadcasts gameplay state/actions.

## Local development

This folder is intentionally single-file friendly so it can be served from GitHub Pages or copied into `/public/plugins/` for local testing.

If you want to preview it standalone, open `index.html` directly or load it through the Memizy multiplayer sandbox.

## Notes

- Keep the data structure flat in the session payload; item fields like `question`, `front`, and `back` are expected at the item root.
- Preserve `MULTI_INIT` handling if you need to support older host builds.
- Update the imported SDK version together with the package release.

# 🎮 Multiplayer Quiz – OQSE Plugin

A real-time multiplayer quiz game for OQSE study sets. Perfect for classroom competitions and study group challenges.

**Live Demo:** [Deploy via GitHub Pages](#deployment)

---

## ✨ Features

- **Real-time Multiplayer:** Teachers host a game, students join with a PIN
- **Live Leaderboard:** See scores update in real-time as students answer
- **Configurable Settings:**
  - ⏱️ Time per question (5–300 seconds)
  - 🔀 Shuffle questions
  - ⚡ Auto-advance after answer reveal
  - 📊 Periodic leaderboard display
- **Multiple Question Types:** MCQ (single/multi), True/False, Short Answer, Matching
- **Rich Content Support:** Markdown and images in questions
- **Mobile-Friendly:** Touch-optimized for tablets and smartphones
- **Offline-First:** Runs entirely in the browser

---

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

---

## 🚀 Deployment

### Option 1: GitHub Pages (Recommended)

1. **Create a new repository** on GitHub:
   ```
   https://github.com/your-org/plugin-multiplayer-quiz
   ```

2. **Add the plugin files:**
   ```
   plugin-multiplayer-quiz/
   ├── index.html          # Main plugin file
   ├── README.md           # This file
   ├── oqse-manifest.json  # Manifest (optional, for static discovery)
   └── LICENSE             # MIT or your chosen license
   ```

3. **Enable GitHub Pages:**
   - Go to Settings → Pages
   - Set "Source" to `main` branch
   - Your plugin will be available at:
     ```
     https://your-org.github.io/plugin-multiplayer-quiz/
     ```

4. **Register in OQSE Plugin Registry** (optional):
   - Submit to the Memizy Plugin Registry to make it discoverable

### Option 2: Cloudflare Pages

1. Connect your GitHub repo to Cloudflare Pages
2. Plugin will be deployed at:
   ```
   https://plugin-multiplayer-quiz.pages.dev/
   ```

### Option 3: Self-Hosted

Serve the `index.html` file on any HTTP server:
```bash
python -m http.server 8080
# Visit: http://localhost:8080/index.html
```

---

## 🔌 Integration

### Host Application (Memizy)

The host loads this plugin in an iframe and communicates via `window.postMessage`:

```javascript
// Host -> Plugin (INIT_SESSION)
iframe.contentWindow.postMessage({
  type: 'INIT_SESSION',
  role: 'host',
  context: {
    pin: 'ABCD',
    playerId: 'host-123',
    playerName: 'Teacher',
    items: [...],  // OQSE items
    assets: {...}, // Metadata
    players: [...],
    settings: {
      timePerQuestion: 20,
      shuffleQuestions: false,
      autoAdvanceReveal: 5,
      showLeaderboardAfterEvery: 0
    }
  }
}, '*');
```

### Plugin → Host (MULTI_BROADCAST)

When a player answers:
```javascript
window.parent.postMessage({
  type: 'MULTI_BROADCAST',
  payload: { /* game state */ }
}, '*');
```

---

## ⚙️ Multiplayer Declaration (Manifest)

The plugin declares multiplayer contract metadata in `appSpecific.memizy.multiplayer`:

```json
{
  "appSpecific": {
    "memizy": {
      "multiplayer": {
        "apiVersion": "0.3",
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
1. Read which multiplayer API contract version the plugin targets
2. Enforce or guide lobby size via the declared players range
3. Apply lobby/runtime behavior such as late-join, reconnect, team UI, and client orientation hints

---

## 📱 Mobile & Accessibility

- ✅ Touch-friendly buttons (44×44 px min)
- ✅ Responsive design (mobile, tablet, desktop)
- ✅ Semantic HTML (`<nav>`, `<main>`, `<article>`)
- ✅ High color contrast (WCAG AA)
- ✅ Keyboard navigation (Tab, Enter, Escape)

---

## 🛠️ Development

### Local Testing

1. Serve the file locally:
   ```bash
   cd multiplayer-quiz
   python -m http.server 8080
   ```

2. Open in browser:
   ```
   http://localhost:8080/index.html
   ```

3. Open DevTools (F12) to inspect:
   ```javascript
   // Check if manifest is discoverable
   document.querySelector('script[type="application/oqse-manifest+json"]')?.textContent
   ```

### Manifest Validation

Verify your manifest is valid JSON:
```bash
node -e "console.log(JSON.parse(document.querySelector('script[type=\"application/oqse-manifest+json\"]').textContent))"
```

---

## 📄 License

[MIT License](LICENSE) – Feel free to fork, modify, and redistribute.

---

## 🤝 Contributing

Issues and pull requests welcome! Please:

1. Test on mobile + desktop
2. Verify OQSE manifest validity
3. Update this README if adding features
4. Follow the [OQSE Specification](https://github.com/memizy/oqse)

---

## 📚 Resources

- **OQSE Spec:** https://github.com/memizy/oqse
- **Memizy:** https://memizy.com
- **Plugin SDK:** https://github.com/memizy/plugin-sdk
- **Manifest Format:** OQSE v1.0 Application Manifest

---

## 📞 Support

- **Issues:** GitHub Issues
- **Discussions:** GitHub Discussions
- **Documentation:** [OQSE Plugin Development Guide](https://oqse.dev/plugins)

---

**Built with ❤️ for the open-source education community.**
