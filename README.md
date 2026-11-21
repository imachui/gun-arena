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
- **GameGold (GGD) Store** (buying): packages are `PACKS` in `index.html` (peso amount + bonus %). The base price is `STORE_BASE` (0.6 GGD per ₱1, so ₱50 = 30 GGD). The admin can change it on the Admin screen ("Coins per ₱1").
- **GameGold (GGD) Market** (withdrawing): the admin sets the value of 1 GGD in pesos on the Admin screen. It is used for withdrawals and the lobby chart, and every change is recorded on the chart.
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
Search `CHESS` in `index.html`. Rooms: friends `rooms/C####`, ranked queue `rooms/QC<bet>_<seconds-per-move>`, matches `rooms/CM######`. Moves are stored in `rooms/<code>/moves/<n>` (and a timestamp); every client replays them with the same rules engine. There is a per-move timer: a timed-out turn is recorded as a deterministic `pass` (or a forced legal move when in check), so both clients write the same record. The board is rendered with three.js (already loaded for Gun Arena). No rules-file changes are needed. The generic `mkQ()` matchmaking helper can be reused for future games.

## Billiards
Search `BILLIARDS` in `index.html`. Rooms: friends `rooms/B####`, ranked queue `rooms/QB<bet>`, matches `rooms/BM######`. The player who shoots runs the physics and applies the 8-ball rules, then publishes the final ball positions (`rooms/<code>/snaps/<n>`); the opponent replays the shot (`rooms/<code>/shots/<n>`) and then snaps to those positions, so both screens always agree.

## Sunday-only cash-out
Withdrawals are only allowed on Sundays in Philippine time (UTC+8). The Withdraw window shows the next cash-out day and locks the button on other days. **Republish `firestore.rules`** too: it now rejects withdrawal requests created on any other day, so the rule cannot be bypassed from the browser.

## Auto GameGold (GGD) Market
The GameGold (GGD) Market price now updates itself every 30 minutes (no admin action needed). It moves smoothly up and down around the **auto market center** you set on the Admin screen (default **₱0.50 per coin**), by up to the **swing %** you choose (default ±20%, so about ₱0.40 to ₱0.60). Every phone computes the same price from the clock, so everyone sees the same number and the chart has a point for every 30 minutes. In Admin you can: change the center price, change the swing %, or turn **Auto market** off to use the fixed value instead. Withdrawals use the live price at the moment the player requests them (it is saved on the request).

## Full screen and landscape (phones)
- Android (Chrome and other browsers): the first tap anywhere puts the game in full screen. Gun Arena, Chess and Billiards then lock the screen to landscape; the lock is released when you leave those games. Tetris Battle stays full screen but follows the phone's rotation (its board is taller than wide, so portrait gives the biggest board).
- Chess in landscape uses a side-by-side layout (players left and right, board in the middle) so the board is as tall as the screen. Billiards in landscape uses the whole screen with the buttons floating over the rails.
- iPhone Safari does not allow web pages to force full screen or lock rotation. Players see a "Rotate your phone" screen (with "Continue anyway") and a one-time tip to use Share > Add to Home Screen; the installed icon opens in true full screen (`apple-mobile-web-app-capable` is set).
- Code: search `Mobile: full screen + landscape` in `index.html`.

## Billiards: target hints
- Each ball has a big number sticker that always faces the camera.
- The top bar shows each player's group as number chips (solids 1-7 or stripes 9-15, then the 8). Chips turn grey when potted; the 8 glows when it is the last ball.
- A goal line says what to pot ("YOUR BALLS: SOLIDS 1-7 - 5 left", "OPEN TABLE...", "POT THE 8-BALL TO WIN!").
- During your aim, a green ring marks every ball you may hit and the other balls are dimmed.
- The 💡 button repeats the rule in words. In practice vs bot it also suggests a ball, highlights the pocket and turns the cue toward it. In bet matches it gives no aim help (fairness).
- Bug fixed: the cue ball used to count as a "solid", so a solids player could never legally pocket the 8-ball.
