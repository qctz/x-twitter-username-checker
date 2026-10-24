# X Username Checker (Browser Console)

A copy-and-paste script that checks whether X (Twitter) usernames are available. It runs in the developer console of any Chromium browser (Opera GX, Chrome, Edge, Brave) and works by reading what the X settings page itself displays, so it doesn't depend on any X API endpoints.

**It never clicks Save, so your own username is never changed.**

---

## Requirements

- A Chromium-based browser (Opera GX, Chrome, Edge, Brave, etc.)
- A logged-in X account
- The script file (`consolev2.txt`)

---

## Quick Start

1. **Log in to X** and open the username settings page:
   ```
   https://x.com/settings/screen_name
   ```
2. **Refresh the page once** (`F5`). This makes sure the page is in a clean state.
3. **Open the console**: press `F12` and click the **Console** tab.
4. **Edit the name list.** Open the script in a text editor and replace the names inside the `NAMES` block with the ones you want to check (one per line).
5. **Paste the whole script** into the console and press **Enter**.
6. Watch the results print live. When it finishes, the available names are copied to your clipboard.

> If your browser blocks pasting into the console, type `allow pasting` and press Enter first, then paste again.

---

## Editing the Name List

Names go inside the backtick block near the top of the script, one per line:

```js
const NAMES = `
CrimsonCartel
GhostSyndicate
RavenSyndicate
`.split("\n")...
```

- A leading `@` is stripped automatically, so `@Name` and `Name` both work.
- Blank lines are ignored.
- Names must be 1-15 characters and contain only letters, numbers, and underscores. Anything else is skipped as `invalid format` before it's ever checked.

---

## Reading the Output

Each name is printed as `[position/total] @name: result`.

| Result | Meaning |
|---|---|
| **AVAILABLE** (green, bold) | No "taken" message appeared and the Save button became enabled |
| **taken** | The page showed a "has been taken" / "already taken" / "not available" message |
| **not allowed (...)** | The page showed some other message (for example a restricted or reserved name). The page text is shown in parentheses |
| **unknown (...)** | No message appeared and Save stayed disabled within the wait time. Try that name again, or check it manually |
| **invalid format, skipped** | The name failed the local format check (length or characters) |

At the end you'll see a summary:

```
Done. Available: 3 | Taken: 15 | Invalid: 1 | Unknown: 1
AVAILABLE:
name1
name2
name3
```

---

## Getting Your Results

- **Clipboard:** available names are copied automatically when the run finishes (if the browser allows it).
- **Console object:** everything is also saved on `window.__results`. Type this in the console afterward:
  ```js
  window.__results.available   // array of available names
  window.__results.taken       // array of taken names
  window.__results.invalid     // array of invalid names
  window.__results.unknown     // array of names that couldn't be determined
  ```
- **Copy again manually:**
  ```js
  copy(window.__results.available.join("\n"))
  ```

---

## Stopping Early

Type this into the console at any time:

```js
window.__stop = true
```

The script finishes the name it's on, then stops and prints the summary.

---

## Settings You Can Adjust

These are near the top of the script:

| Setting | Default | What it does |
|---|---|---|
| `DELAY_MIN` / `DELAY_MAX` | 2000 / 4000 ms | Random pause between names. Increase these for long lists to be gentler on the site |
| `MAX_WAIT` | 7000 ms | How long to wait for the page to show a verdict before marking a name `unknown` |

---

## How It Works

For each name, the script:

1. Clears the username box, then types the name in using the same events a real keystroke would trigger.
2. Waits for the page to run its own availability check.
3. Decides the result:
   - A "taken" message appears: **taken**
   - The Save button becomes enabled (and, after a short extra wait, no late "taken" message shows up): **available**
   - Neither happens before the timeout: **invalid** (if new page text appeared) or **unknown**
4. Waits a random delay, then moves on to the next name.

---

## Troubleshooting

**"Username box not found. Open x.com/settings/screen_name first."**
You're not on the right page, or it hasn't finished loading. Navigate to `https://x.com/settings/screen_name`, refresh, and run the script again.

**Lots of `unknown` results**
- Make sure you refreshed the page before running.
- Increase `MAX_WAIT` (for example to `10000`).
- Increase the delays between names.
- X may have changed its page layout. The script looks for an input named `typedScreenName` and a button labeled "Save".

**Everything shows as taken (or everything shows as available)**
The page text or button labels may differ in your language or after an X update. The script matches English text ("has been taken", "Save"). Set your X language to English, or adjust the `takenShown` and `saveBtn` checks.

**Clipboard copy didn't work**
Use `window.__results.available` from the console instead.

**Console won't accept pasted text**
Type `allow pasting` and press Enter, then paste again.

---

## Notes and Cautions

- **Automation:** this script automates typing on X's settings page. Automated activity can conflict with X's terms, and heavy use may trigger rate limits or temporary restrictions on your account. Keep lists modest and the delays generous.
- **Availability isn't a reservation.** A name that shows as available can be taken by someone else before you claim it.
- **Results are only as good as the page.** If X changes its settings page, the detection logic may need updating.
- Don't leave the page or switch accounts while the script is running.