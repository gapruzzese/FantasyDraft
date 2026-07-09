# Draft Simulator

A snake/normal draft app. You create a draft, send each player a private link to enter their top-25 rankings, and once everyone has submitted, the app randomizes the order and auto-drafts: on each turn a player gets their highest-ranked player who hasn't been taken yet.

Data is stored in **Firebase Firestore** so the shared links work across everyone's devices.

---

## Part 1 — Set up Firebase (one time, ~5 min, free)

1. Go to https://console.firebase.google.com and click **Add project**. Give it any name, disable Google Analytics if you want (not needed), and create it.
2. In the left sidebar, open **Build → Firestore Database** and click **Create database**.
   - Choose **Start in test mode** (fine for a casual draft — see the note below), pick a location, and enable.
3. Back on the project overview, click the **Web** icon (`</>`) to register a web app. Give it a nickname and register. **Do not** enable Firebase Hosting.
4. Firebase shows you a `firebaseConfig` object. Copy it.

### Add the config to the app
Open `index.html`, find this block near the top of the `<script type="module">` section:

```js
const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  ...
};
```

Replace it with the config you copied from Firebase. Save.

### Firestore security rules (recommended)
Test mode lets anyone read/write for 30 days, then locks. For a simple always-open setup, go to **Firestore → Rules** and paste:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /drafts/{doc} {
      allow read, write: if true;
    }
  }
}
```

This lets anyone with a draft link read/write that draft. Fine for a friendly draft among friends. Don't store anything sensitive.

---

## Part 2 — Host it on GitHub Pages

1. Create a new repository on GitHub (e.g. `draft-simulator`).
2. Upload **`index.html`** (drag-and-drop into the repo works). Make sure the file is named exactly `index.html` and is in the repo root.
3. Go to the repo's **Settings → Pages**.
4. Under **Build and deployment**, set **Source = Deploy from a branch**, **Branch = main**, **Folder = / (root)**. Save.
5. Wait ~1 minute. Your app is live at:
   `https://YOUR-USERNAME.github.io/draft-simulator/`

That's it. Open the URL, create a draft, and share the per-player links.

---

## How to run a draft
1. Open your GitHub Pages URL — this is the **control page**.
2. Set number of players, rounds, and Snake or Normal. Click **Create Draft**.
3. Copy each **player link** and send it to that person.
4. Each player opens their link, enters their top-25 rankings (blanks are OK), and submits.
5. On the control page, click **Refresh** to see who's submitted.
6. Once all have submitted, click **Randomize & Run Draft**. Order is shuffled and results appear by round.

Notes:
- Duplicate names are matched case-insensitively, so "Josh Allen" and "josh allen" won't both get drafted.
- If a player runs out of available ranked choices on a turn, that slot shows "(no available ranked choice)".
- Re-running reshuffles and recomputes from the same rankings.
