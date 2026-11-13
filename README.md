# AI Divide, Trust, and Governance: interactive slides with live polling

Two pages and one folder:

- `index.html` is the presentation you show on the projector.
- `vote.html` is the page the audience opens on their phones (the slides show a QR code for it).
- `slides/` holds the slide images.

Keep the three together.


## 1. Put it on GitHub Pages (about 5 minutes)

1. On github.com, create a new **public** repository, for example `ai-divide`.
2. Click **Add file > Upload files**, drag in `index.html`, `vote.html`, `README.md` and the whole `slides` folder, then **Commit changes**.
3. Go to **Settings > Pages**. Under "Build and deployment", choose **Deploy from a branch**, branch **main**, folder **/ (root)**, and **Save**.
4. After a minute or two the site is live at `https://YOUR-USERNAME.github.io/ai-divide/`.

At this point the slides work everywhere and the polls run in **hand-count mode**: ask for a show of hands and click an option to add a vote. Results are kept in your browser.

## 2. Turn on phone voting (about 10 minutes, free)

GitHub Pages can show pages but cannot store votes, so phone voting needs a small free database. This site uses Google's Firebase Realtime Database.

1. Go to https://console.firebase.google.com and sign in with a Google account.
2. **Create a project** (any name; you can turn Google Analytics off).
3. In the left menu open **Build > Realtime Database** and click **Create Database**. Pick any location and start in **locked mode**.
4. Open the **Rules** tab, replace everything with the block below, and click **Publish**:

```json
{
  "rules": {
    "aidivide": {
      "$session": {
        ".read": true,
        ".write": true,
        "votes": { "$q": { "$device": { ".validate": "newData.isNumber() && newData.val() >= 0 && newData.val() <= 9" } } },
        "words": { "$device": { ".validate": "newData.isString() && newData.val().length <= 60" } },
        "qa":    { "$id": { ".validate": "newData.child('t').isString() && newData.child('t').val().length <= 300" } }
      }
    }
  }
}
```

5. Open the **Data** tab and copy the database address at the top. It looks like
   `https://your-project-default-rtdb.firebaseio.com` or `https://your-project-default-rtdb.REGION.firebasedatabase.app`.
6. Open your site, click **⚙ Polls** (or press **S**), paste the address under "Phone voting database" and click **Save and test**. You should see "Connected. Reading and writing both work."

The address is remembered in that browser. To make it permanent for any computer, edit `index.html` on GitHub (pencil icon) and paste it between the quotes in the line near the top:

```js
const CONFIG = { databaseURL: "", session: "main", publicURL: "" };
```

## 3. Presenting

| Key | What it does |
| --- | --- |
| → , Space, clicker | Next slide |
| ← | Previous slide |
| R | Show or hide poll results |
| G | All slides |
| N | Notes panel |
| P | Speaker view in a second window (notes, next slide, timer) |
| F | Full screen |
| S | Polling settings |

On poll slides the audience scans the QR code. Their phones follow you to whichever poll slide is on screen. Results stay hidden until you press **R**.

- **Slide 3, 20, 34**: two multiple-choice questions each.
- **Slide 35**: word cloud. Click a word to remove it.
- **Slide 36**: audience questions. Click ✕ to remove one.
- **✋ Hand count** lets you add votes by clicking an option (Shift-click removes one), for people without a phone.

## 4. Presenting in more than one place

Open **⚙ Polls**:

- **Session**: type a new name for each venue (for example `taipei-nov18`, `ntnu`) and click **Use this session**. Each session has its own QR code and its own results, so earlier results are kept and you can switch back to them.
- **Reset ALL results** erases the current session so the same audience link starts from zero. Phones that already voted can vote again.
- **Reset this poll** (on each poll slide) clears just that slide.
- **Download results (CSV)** saves the counts and the open responses for the current session.

## Good to know

- Voting is anonymous. No names, emails or accounts are collected. Each browser gets one answer per question, which it can change.
- The database rules above let anyone with the link write votes, which is what makes account-free voting possible. Do not store anything else in that database.
- Firebase's free plan allows 100 simultaneous connections. Phones here send short requests instead of holding a connection open, which is the approach Firebase recommends for this limit, but try it with a group before a very large audience.
- Slides other than the poll slides are images made from the PowerPoint file. When the deck changes, the images need to be regenerated.
