# Kaun Banega 100 Crore Pati 🎬

A KBC-style quiz game — 15 questions, 5 lifelines, ₹100 Crore mega prize.
Single HTML file, no build step. Play it live:

**https://nipjyoti85.github.io/kbc-100-crore/**

## Scoreboard

- Every finished game is recorded; players whose **final winnings are ₹25 Lakh or more** appear on the 🏆 SCOREBOARD (name, date, prize), ranked by amount.
- Without any setup, scores are stored in each player's own browser (localStorage) — each device sees only its own winners.

## Make the scoreboard GLOBAL (all players see the same board)

The game has built-in support for a shared online scoreboard using **Firebase Realtime Database** (free). Once connected, every player on every device sees the same top-winners board, plus a live count of games and players.

Setup (~5 minutes):

1. Go to <https://console.firebase.google.com> and sign in with your Google account.
2. Click **Create a project** → name it anything (e.g. `kbc-100-crore`). Google Analytics is not needed — you can disable it.
3. In the left menu: **Build → Realtime Database → Create Database** → choose a location → start in **test mode**.
4. Copy the database URL shown at the top of the Data tab. It looks like:
   `https://kbc-100-crore-xxxxx-default-rtdb.firebaseio.com`
5. Open `index.html`, find this line near the top of the `<script>` section, and paste your URL:

   ```js
   const REMOTE_DB = "https://kbc-100-crore-xxxxx-default-rtdb.firebaseio.com";
   ```

6. Commit/upload the change. Done — the scoreboard is now worldwide.

### Recommended database rules

Test mode expires after 30 days and allows anyone to edit or delete data. Replace the rules
(Realtime Database → **Rules** tab) with these — they let the game add scores and read the board,
but nobody can modify or delete existing scores:

```json
{
  "rules": {
    ".read": false,
    ".write": false,
    "kbc": {
      "scores": {
        ".read": true,
        "$id": {
          ".write": "!data.exists() && newData.exists()",
          ".validate": "newData.hasChildren(['n','p','v','d','t']) && newData.child('n').isString() && newData.child('n').val().length > 0 && newData.child('n').val().length <= 20 && newData.child('p').isString() && newData.child('p').val().length <= 12 && newData.child('v').isNumber() && newData.child('d').isString() && newData.child('d').val().length <= 12 && newData.child('t').isNumber()"
        }
      }
    }
  }
}
```

If the database is unreachable (or `REMOTE_DB` is left empty), the game automatically falls back
to the device-local scoreboard — nothing breaks.
