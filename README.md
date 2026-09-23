[README.md](https://github.com/user-attachments/files/32579862/README.md)
# UCSF Surgery Quality Org Chart: setup guide

Three parts: a Google Sheet holds the roster, a small Apps Script turns the sheet into an API, and GitHub Pages hosts the chart page. Total setup is about 20 minutes. Checkpoint after each part: you'll have something working you can look at.

## Part 1: the Google Sheet (5 min)

1. Go to sheets.google.com and create a blank spreadsheet. Name it something like "Surgery Quality Org Chart".
2. Rename the first tab to exactly `Roster` (double-click the tab name at the bottom).
3. File > Import > Upload > choose `roster.csv` from this folder. Import location: **Replace current sheet**. Separator: comma. Click Import data.
4. You should see 153 rows with these columns: Section, Division, Name, Title, Role, QualityRep, MMLeader, Notes, ID.

Checkpoint: the whole faculty roster is now in a sheet you can edit directly. Put a `Y` in QualityRep or MMLeader for anyone and it will show in the chart.

Column notes:
- **Section** is one of `Chair`, `Vice Chair Quality`, `Support`, `Vice Chairs`, or `Division`.
- **Role** is `chief`, `section-chief`, `faculty`, or `staff`.
- **ID** is filled by the chart automatically; leave it blank for rows you add by hand.

## Part 2: the Apps Script API (10 min)

1. In the spreadsheet, open Extensions > Apps Script.
2. Delete whatever is in the editor and paste the full contents of `Code.gs`.
3. Near the top, change `PASSPHRASE = 'change-me-before-deploying'` to a passphrase your editors will share. Anyone who has the page URL and this passphrase can save changes.
4. Click the save icon. Optional: pick `testRead` in the function dropdown and click Run to confirm it reads the sheet (you'll be asked to authorize the script the first time; this is normal since it's your own script accessing your own sheet).
5. Click **Deploy > New deployment**. Click the gear next to "Select type" and choose **Web app**.
   - Description: anything
   - Execute as: **Me**
   - Who has access: **Anyone**
6. Click Deploy, authorize when prompted, then copy the **Web app URL**. It ends in `/exec`. Keep it handy.

Checkpoint: paste the Web app URL into a browser tab. You should see the roster as JSON text. If you see it, the API works.

Important: any time you edit Code.gs later, you must Deploy > Manage deployments > edit (pencil) > Version: New version > Deploy, or the live URL keeps running the old code.

## Part 3: GitHub Pages (5 min)

1. On github.com, create a new repository (any name, e.g. `surgery-quality-chart`). It can be public or private; GitHub Pages on a free personal account requires a **public** repo. The page contains only names and titles already public on the UCSF website, and the passphrase is never stored in the page.
2. Open `docs/index.html` in a text editor. Near the top of the script, set:
   ```
   const API_URL='PASTE-YOUR-WEB-APP-URL-HERE';
   ```
3. Upload the `docs` folder to the repository (Add file > Upload files, drag the `docs` folder in, commit).
4. In the repository, go to Settings > Pages. Under Build and deployment, Source: **Deploy from a branch**; Branch: **main**, folder: **/docs**. Save.
5. After a minute or two, the page URL appears at the top of the Pages settings (it looks like `https://YOURNAME.github.io/surgery-quality-chart/`).

Checkpoint: open that URL. The subtitle should say "Changes save to the shared Google Sheet". Edit someone, and the first save prompts for the passphrase once per browser.

## Sharing with editors

Send them the GitHub Pages URL and the passphrase. That's it. They don't need Google or GitHub accounts. Changes appear for other editors within about 30 seconds, or immediately on reload.

For view-only recipients, use the **Print / PDF** button and send the PDF.

## Troubleshooting

- **"Could not reach the shared sheet"**: the API_URL in index.html is wrong, or the deployment isn't set to "Anyone". Re-check Part 2 step 5 and the checkpoint.
- **"Save rejected: bad passphrase"**: the page clears the stored passphrase and will ask again on the next save.
- **Two people edit the same moment**: last save wins. If an edit disappears, that's why; the sheet's File > Version history lets you recover anything.
- **Someone edited the sheet directly and the chart looks wrong**: check that Section and Role values match the allowed words above (case doesn't matter).
