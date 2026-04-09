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
- **Rich Content Support:** Markdown, HTML, images in questions
- **Mobile-Friendly:** Touch-optimized for tablets and smartphones
- **Offline-First:** Runs entirely in the browser

---

## 📋 OQSE Compatibility

This plugin implements the **OQSE (Open Quiz & Study Exchange) v1.0** specification.

| Property | Value |
|----------|-------|
| **Manifest Version** | 1.0 |
| **Plugin Version** | 1.0.0 |
| **Min OQSE Version** | 1.0 |
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
- **Features:** Markdown, HTML sanitization
- **Item Properties:** Explanations, source attribution
- **Metadata Properties:** Description, author, license

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
// Host → Plugin (MULTI_INIT)
iframe.contentWindow.postMessage({
  type: 'MULTI_INIT',
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

## ⚙️ Settings Schema

The plugin declares a Memizy-specific settings schema in its OQSE manifest:

```json
{
  "appSpecific": {
    "memizy": {
      "settingsSchema": [
        {
          "id": "timePerQuestion",
          "label": "Time per question (seconds)",
          "type": "number",
          "defaultValue": 20,
          "min": 5,
          "max": 300
        },
        // ... more settings
      ]
    }
  }
}
```

Host applications can:
1. Parse this schema from the manifest
2. Render a dynamic settings form
3. Pass configured values back in `MULTI_INIT`

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
