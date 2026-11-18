# Gun Arena

3D top-down arena shooter plus 1v1 Tetris Battle (both play to earn). Practice vs bots (no sign-in) or online rooms (Google sign-in, Firebase).

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
- **Coin Store** (buying): packages are `PACKS` in `index.html` (peso amount + bonus %). The base price is `STORE_BASE` (0.6 coins per ₱1, so ₱50 = 30 coins). The admin can change it on the Admin screen ("Coins per ₱1").
- **Coin Market** (withdrawing): the admin sets the value of 1 coin in pesos on the Admin screen. It is used for withdrawals and the lobby chart, and every change is recorded on the chart.
- Both are saved in Firestore at `settings/economy`, so you must republish `firestore.rules`.
- Matches: winners get 2x their bet, no house cut.

## Deploy on GitHub Pages
Repo > Settings > Pages > Source: Deploy from a branch > `main` / `(root)` > Save.
Your game: `https://YOURNAME.github.io/REPO/`

## Security note
Prize payouts and match results are decided in the players' browsers. Fine for friends and testing.
Before real money, move payouts and room settlement to a Cloud Function.

## Matchmaking
Ranked "Find match" uses a queue stored under `rooms/Q<bet>` in the Realtime Database, so the existing `database.rules.json` already works. Players with the same bet wait together. The longest-waiting player acts as the matchmaker: once two players are found a 15-second countdown starts, and then 2-3 players start a 1v1, 4-7 start a 2v2 and 8 start a 4v4 (leftover players keep waiting). Friends rooms are created separately with a 4-digit code.

## Tetris Battle
Added inside `index.html` (search for `TETRIS BATTLE`). Players choose Gun Arena or Tetris Battle at the top of the lobby. It uses the same coins, bets, 2x payout and RP as Gun Arena, so **no rules files need to change**.
- Rooms live in the same Realtime Database `rooms/` path: friends rooms are `rooms/T####`, ranked queues are `rooms/QT<bet>`, and matches are `rooms/TM######`.
- Both players get the same piece sequence (shared seed). Boards, scores and garbage are synced through the room; the lowest connected player acts as referee (top-out, disconnect, or higher score at 5:00).
- Same security note as Gun Arena: results are decided in the browsers, so move payouts to a Cloud Function before taking real money.

## Chess
Search `CHESS` in `index.html`. Rooms: friends `rooms/C####`, ranked queue `rooms/QC<bet>_<timecontrol>`, matches `rooms/CM######`. Moves are stored in `rooms/<code>/moves/<n>` with the clocks after each move; every client replays them with the same rules engine. Time-outs, resignations and draws are written by the clients that detect them. No rules-file changes are needed. The generic `mkQ()` matchmaking helper can be reused for future games.
