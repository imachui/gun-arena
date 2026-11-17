# Gun Arena

3D top-down arena shooter. Practice vs bots (no sign-in) or online rooms (Google sign-in, Firebase).

## Files
- `index.html`: the whole game (put it at the repo root)
- `logo.png`: the original logo file (optional). A copy is already embedded inside `index.html`
- `config.js`: your Firebase config, `ADMIN_UIDS`, GCash number and name
- `MECHANICS.md`: how to play and earn, ready to post on social media (Filipino)
- `firestore.rules`: paste into Firebase Console > Firestore > Rules
- `database.rules.json`: paste into Firebase Console > Realtime Database > Rules

## Setup (once)
1. Firebase Console: create a project, add a Web app, copy `firebaseConfig` into `config.js`.
2. Authentication > Sign-in method > enable Google.
3. Create Firestore and Realtime Database (Singapore).
4. Publish both rules files. In `firestore.rules`, replace the admin UID with yours.
5. In `config.js`, set `ADMIN_UIDS`, `GCASH_NUMBER`, `GCASH_NAME`. In `index.html`, review `TIERS` and `BETS`.
6. Authentication > Settings > Authorized domains > add `YOURNAME.github.io`.

## Economy
- The admin sets the value of 1 coin (in pesos) on the Admin screen. The same value is used for the Coin Store and for withdrawals, and every change is recorded on the Coin Market chart in the lobby.
- It is saved in Firestore at `settings/economy`, so you must republish `firestore.rules`.
- Default if nothing is saved: 1 coin = ₱1. Store packages are the peso amounts in `PACKS` in `index.html`.
- Matches: winners get 2x their bet, no house cut.

## Deploy on GitHub Pages
Repo > Settings > Pages > Source: Deploy from a branch > `main` / `(root)` > Save.
Your game: `https://YOURNAME.github.io/REPO/`

## Security note
Prize payouts and match results are decided in the players' browsers. Fine for friends and testing.
Before real money, move payouts and room settlement to a Cloud Function.
