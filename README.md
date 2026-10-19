# Voice Expense Logger

A personal expense app for iPhone. You speak an expense, it saves it, converts foreign currency to JPY at the daily European Central Bank rate, and archives to Google Sheets in your Google Drive.

It runs from GitHub Pages and opens from an icon on your iPhone's home screen. Your expenses are stored on the iPhone itself; nothing is sent anywhere except the archive files you send to your own Google Drive.

Setup takes about 30 minutes, once. Steps 1, 2 and 5 are enough to start logging expenses. Steps 3 and 4 add the Google Drive archive.

---

## 1. Put the app on GitHub Pages (10 minutes)

1. Sign in at **github.com** (create a free account if you don't have one).
2. Top right, click **+** → **New repository**.
   - Repository name: `expenses` (any name works; it becomes part of your web address).
   - Choose **Public**. Free GitHub Pages needs a public repository. That's fine: the code holds no personal data, and your expenses never go to GitHub.
   - Click **Create repository**.
3. On the new repository page, click **uploading an existing file**.
4. Drag in **all the files from this package** (index.html, config.js, manifest.webmanifest, sw.js, README.md and the four .png icons). Click **Commit changes**.
5. Open **Settings** → **Pages** (left menu).
   - Under "Build and deployment", Source: **Deploy from a branch**.
   - Branch: **main**, folder **/ (root)**. Click **Save**.
6. Wait 1–2 minutes and refresh the page. GitHub shows your address, for example:
   `https://YOUR-USERNAME.github.io/expenses/`

Write that address down. You need it in step 3.

## 2. Check it in a browser

Open your address on your PC. You should see the app. Type `Lunch 980 yen` and tap **Add expense**. The top right should show "Rates" with a date once exchange rates load.

## 3. Google Cloud setup for Drive archive (15 minutes, once)

Do this on your PC at **console.cloud.google.com**, signed in with the Google account whose Drive you want to use.

**3a. Create a project**
1. Top bar: click the project picker → **New project**. Name it `Expense Logger` → **Create**. Make sure it's selected in the top bar.
2. Open **Home / Dashboard** (or the project picker) and note the **Project number** (digits only, for example `123456789012`). This is your `GOOGLE_APP_ID`.

**3b. Turn on three APIs**

Menu → **APIs & Services** → **Library**. Search for each and click **Enable**:
- **Google Drive API**
- **Google Sheets API**
- **Google Picker API**

**3c. Consent screen**
1. **APIs & Services** → **OAuth consent screen** (may be shown as **Google Auth Platform**). Click **Get started**.
2. App name `Expense Logger`, support email: yours. Audience: **External**. Contact email: yours. Agree and **Create**.
3. Go to **Audience** → **Test users** → **Add users** → add your own Gmail address → **Save**.
   Leave the app in **Testing**. You don't need Google's verification for personal use.
   - In testing mode Google may ask you to sign in again about once a week. That's normal.
4. Go to **Data access** → **Add or remove scopes** → tick `.../auth/drive.file` ("See, edit, create and delete only the specific Google Drive files you use with this app") → **Update** → **Save**.

**3d. Client ID**
1. **APIs & Services** → **Credentials** (or **Clients**) → **Create credentials** → **OAuth client ID**.
2. Application type: **Web application**. Name: `Expense Logger web`.
3. **Authorized JavaScript origins** → Add URI: `https://YOUR-USERNAME.github.io` (no path, no trailing slash).
4. **Authorized redirect URIs** → Add URI: your full app address with the trailing slash, for example `https://YOUR-USERNAME.github.io/expenses/`
5. **Create**. Copy the **Client ID** (ends with `.apps.googleusercontent.com`). This is your `GOOGLE_CLIENT_ID`.

**3e. API key (for the file picker)**
1. **Credentials** → **Create credentials** → **API key**. Copy it. This is your `GOOGLE_API_KEY`.
2. Click the key to edit it:
   - Application restrictions: **Websites** → add `https://YOUR-USERNAME.github.io/*`
   - API restrictions: **Restrict key** → select **Google Picker API** only → **Save**.

## 4. Put the keys in config.js

1. In your GitHub repository, click **config.js** → the pencil icon (Edit).
2. Fill in the three values between the quotes:

```js
window.APP_CONFIG = {
  GOOGLE_CLIENT_ID: "1234567890-abc123.apps.googleusercontent.com",
  GOOGLE_API_KEY: "AIzaSy...",
  GOOGLE_APP_ID: "123456789012"
};
```

3. Click **Commit changes**. Wait a minute for GitHub Pages to update.

These values are not secrets. Google only accepts them from your GitHub Pages address, and only your test-user account can sign in.

## 5. Install on your iPhone

1. Open **Safari** (it must be Safari) and go to your app address.
2. Tap the **Share** button (square with an up arrow) → **Add to Home Screen** → **Add**.
3. Open the app from the new **Expenses** icon from now on.

Important: the icon app keeps its own storage, separate from Safari. Always use the icon. Expenses you add in a Safari tab won't appear in the icon app.

## 6. First use on the iPhone

- **Speaking:** tap the round microphone button and allow microphone and speech recognition when asked. If the button doesn't respond, tap the text box and use the microphone key on the keyboard instead (turn on Settings → General → Keyboard → **Enable Dictation**).
- **Exchange rates:** loaded automatically when you're online and cached for offline use.
- **Archive:** Archive tab → **Sign in with Google** → choose your account. Google warns that the app isn't verified: tap **Continue** (it's your own app). Then **Choose folder** or **Choose existing sheet**, and tap **Archive**.
- **Moving records from the claude.ai version:** in the claude.ai version, go to Reports → **Download … as CSV** for each month. Save the file to the Files app. Then in this app: Settings → **Import file** → pick the CSV.

## Keeping your data safe

Expenses live only on this iPhone. If you delete the home-screen icon or reset Safari data, unarchived records are gone.
- Archive to Drive at least monthly, or
- Settings → **Export backup** and save the file to iCloud Drive. **Import file** restores it.

## Updating the app later

When you replace `index.html` or another file on GitHub, also open `sw.js` and change `VERSION = "vel-v1"` to `"vel-v2"` (and so on). The iPhone picks up the new version the next time you open the app twice.

## Troubleshooting

| Problem | Fix |
|---|---|
| "Error 400: redirect_uri_mismatch" when signing in | The redirect URI in step 3d must match your app address exactly, including the trailing `/`. |
| "Access blocked: app has not completed verification" | Add your Gmail as a test user (step 3c-3). |
| The file picker doesn't open or shows an error | Check the API key's website restriction and that Google Picker API is enabled (3b, 3e). Check `GOOGLE_APP_ID` is the project **number**, not the project name. |
| "This app can't open that file any more" | Choose the folder or sheet again with the Choose buttons. |
| No "Add to Home Screen" | You're not in Safari, or you're in a private tab. |
| Changes on GitHub don't show on the iPhone | Bump `VERSION` in `sw.js`, then open the app, close it, and open it again. |
