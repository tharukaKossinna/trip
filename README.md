# Trip Board

A shared planner for a group trip: calendar, activities, accommodation and expenses. Nobody needs an account. Anyone with the trip link can view and add to it.

It's one file, `index.html`, hosted free on GitHub Pages. It saves to a free Firebase database that you own. Setup takes about 10–15 minutes and you only do it once.

Want to see it first? Open `index.html` and add `#demo` to the end of the address. The demo uses example data and saves nothing.

---

## 1. Create the database (Firebase)

1. Go to <https://console.firebase.google.com> and sign in with a Google account.
2. Click **Create a project**. Name it something like `trip-board`, turn **off** Google Analytics, and create it.
3. In the left menu, open **Build → Firestore Database** and click **Create database**.
   - If it asks for an edition, pick **Standard**.
   - Location: **australia-southeast1 (Sydney)**.
   - Choose **Start in production mode**.
4. Open the **Rules** tab, replace everything with the rules below, and click **Publish**.

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    // Each trip lives at /trips/{tripId}. The long random code in the
    // invite link keeps a trip private, and trips can't be listed.
    match /trips/{tripId} {
      allow get, create, update: if tripId.size() >= 16;
      allow list, delete: if false;

      match /{col}/{docId} {
        allow read, delete: if tripId.size() >= 16
          && col in ['members', 'activities', 'stays', 'expenses'];
        allow create, update: if tripId.size() >= 16
          && col in ['members', 'activities', 'stays', 'expenses']
          && request.resource.data.size() <= 30;
      }
    }
  }
}
```

5. Click the gear icon next to **Project Overview → Project settings**. Under **Your apps**, click the web icon `</>`.
   - Give it a nickname, such as `trip-board`. Leave Firebase Hosting unticked.
   - Click **Register app**.
6. Firebase shows a `firebaseConfig` block. Open `index.html` in a text editor (VS Code, Notepad or TextEdit in plain-text mode). Near the top of the `<script>` section, find this block:

```js
const firebaseConfig = {
  apiKey: "",
  authDomain: "",
  ...
};
```

   Paste your values between the quotes and save the file.

## 2. Put it online (GitHub Pages)

1. On <https://github.com>, click **New repository**. Name it `trip-board`, set it to **Public**, and create it.
2. Click **uploading an existing file**. Drag in `index.html` (and this README if you like), then click **Commit changes**.
3. Go to **Settings → Pages**. Under **Source**, choose **Deploy from a branch**, then branch **main** and folder **/ (root)**. Click **Save**.
4. After a minute or two, the site is live at `https://YOUR-USERNAME.github.io/trip-board/`.

To change the site later, edit `index.html` on GitHub, or upload a new copy. The trip data lives in Firebase, so it's kept when you update the page.

Without Git, there's a quicker option: drag the folder onto <https://app.netlify.com/drop>.

## 3. Start the trip

1. Open your site and click **Start a new trip**.
2. Add your name and colour, then the trip name, destination, dates and currency.
3. Click **Invite**, copy the link, and send it to the group chat.

Friends open the link and pick their name, or add themselves. Each phone or laptop remembers who they are.

---

## Good to know

- **The link is the key.** Anyone who has the trip link can see and edit the plan. Don't post it publicly. Don't save passport numbers, card details or anything else you wouldn't put in a group chat.
- **The Firebase config isn't a password.** It's normal for it to be visible in the page. The database rules and the random trip code are what protect the data.
- **One currency per trip.** Set it in trip details. Convert foreign spending before you enter it.
- **Expenses split evenly** between the people ticked. To split unevenly, log it as separate expenses.
- **Settle up** suggests the fewest paybacks. Tap **Mark as paid** once the money is sent, and the balances update for everyone.
- **New phone?** Open the link and pick your name again.
- **Cost.** Firebase's free plan has daily limits far beyond what a trip group uses, so you won't be charged unless you add billing yourself.
- **Optional extra lock-down.** In Google Cloud Console → APIs & Services → Credentials, you can restrict the Firebase API key to your site's address (`https://YOUR-USERNAME.github.io/*`).
