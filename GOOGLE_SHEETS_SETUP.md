# Google Sheets Backend Setup

This app can keep static data in GitHub and store mutable data in Google Sheets.

## 1. Create Worksheets

Create one Google Spreadsheet and add these worksheets with the exact names and header rows:

`users`

```text
user_id,user_name,total_points
```

`predictions`

```text
user_id,match_id,home_goals,away_goals,penalty_winner
```

`results`

```text
match_id,home_goals,away_goals,penalty_winner,home_yellow_cards,home_indirect_red_cards,home_direct_red_cards,away_yellow_cards,away_indirect_red_cards,away_direct_red_cards
```

`drafts`

```text
draft_id,user_id,user_name,predictions_json
```

`config`

```text
key,value
submissions_open,true
```

`leaderboard`

```text
rank,user_id,user_name,total_points
```

## 2. Create Google Credentials

1. Create a Google Cloud project.
2. Enable the Google Sheets API.
3. Create a service account.
4. Create a JSON key for that service account.
5. Share the Google Spreadsheet with the service account email as Editor.

## 3. Add Streamlit Secrets

In Streamlit Cloud, open the app settings and add secrets using the structure from:

```text
.streamlit/secrets.example.toml
```

Do not commit `.streamlit/secrets.toml`.

## 4. Keep Static Data in GitHub

These files still come from the repository:

```text
data/teams.csv
data/matches.csv
data/knockout_matchups.csv
data/third_place_combinations.csv
logos/
```

When `GOOGLE_SHEETS_BACKEND` is `true`, these mutable tables come from Google Sheets:

```text
users
predictions
results
drafts
config
leaderboard
```

## 5. Cache Behavior

The app caches Google Sheets reads for display and analysis for 10 seconds to avoid hitting API quotas.

Writes do not use cached base data. Before saving users, predictions, or drafts, the app reads the current sheet directly, merges the change, writes the sheet, and clears the Streamlit data cache. This prevents a stale cached table from overwriting another participant's recent submission.

Draft autosaves are throttled to at most once every 5 seconds in Google Sheets mode. Final prediction submission still writes immediately.
