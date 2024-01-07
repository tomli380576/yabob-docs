---
icon: gear
order: 10
---

# Developer Setup Guide

## System Requirements

Install [Node.js](https://nodejs.org/en/), select the `Long Term Support` version.

### Before we start

For the rest of this guide, we will assume that you are setting up for **development use**. For production:
- Fill in `YABOB/src/environment/production.json` instead of `YABOB/src/environment/development.json`
- The run command is `npm run prod` instead of `npm run dev`
- Use `npm install --production` to skip installing dev dependencies

## Step 1. Discord Developer Account, BOB App ID

1. Make a discord server.

2. Follow [Discord's Official Documentation](https://discordpy.readthedocs.io/en/latest/discord.html) and create a bot account. For the bot permissions at step 6, choose **Administrator**.

3. Look for `APPLICATION ID` in the **General Information** tab. This is your `YABOB_APP_ID`.

4. Look for `TOKEN` in the **Bot** tab (it has a reset token button next to it). This is your `YABOB_BOT_TOKEN`.

5. In the **Bot** tab under **Privileged Gateway Intents**, enable **SERVER MEMBERS INTENT**.

6. In the OAuth2 $\to$ URL Generator tab, select **`bot`** in scopes, then select **`Administrator`** in bot permissions. Finally copy the generated URL at the bottom and paste it in your browser.

7. Opening the link will prompt you to invite your YABOB instance! Simply choose the server that you want to invite YABOB to.

8. If you are inviting YABOB to a server with existing roles, YABOB will prompt you to give it the highest role. Once you have saved the role settings, YABOB will DM you again saying that it got the highest role. **Make sure that YABOB's role name is the same as YABOB's username.**

### Clone the source code

``` bash
git clone https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot.git
```

- For simplicity we'll be referring to the project's root directory as YABOB for the rest of this page.

## Step 2. Setup Firebase

1. Go to [Google Firebase Console](https://firebase.google.com/) and sign in with a google account.

2. Click **CREATE A PROJECT** and follow the guide.

3. One the left $\to$ All products $\to$ Cloud Firestore, create a new database. Wait for it to finish.

4. One the left, Settings $\to$ Project Settings $\to$ Service Accounts $\to$ Generate New Private Key. This will prompt you to download a JSON file.

5. Go to `YABOB/src/environment/production.json`

    ```json
    "firebaseCredentials": {
        "projectId": "",
        "privateKey": "",
        "clientEmail": ""
    }
    ```

6. From the file you just downloaded:

    - Put the value of `project_id` into `projectId`
    - Put the value of `private_key` into `privateKey`
    - Put the value of `client_email` into `clientEmail`

## Step 3. Running YABOB without extensions

1. Go to `YABOB/src/environment/production.json`, fill in:

    ```json
    "discordBotCredentials": {
        "YABOB_APP_ID": "",
        "YABOB_BOT_TOKEN": ""
    }
    ```

    For example:

    ``` json
    "discordBotCredentials": {
        "YABOB_APP_ID": "1234567890",
        "YABOB_BOT_TOKEN": "qwertyuoikhjgSDJAHGiqdhqweKDSHahjsgdzxvcbzxvcmbn"
    }
    ```

2. Install all the dependencies:

    ``` bash
    npm install
    ```

3. Now you can run the bot with the fundamental functionalities using this command: (**Don't forget the space between the 2 dashes and `noExtensions`**)

    ```bash
    npm run dev -- noExtensions=true
    ```

## Step 4. Setting up extensions (optional)

Right now you have a functioning YABOB! To make YABOB more useful and customizable, we developed the extensions feature in v4. YABOB comes with 2 built in extensions:

1. **Google Sheet Logging**

   Utilizes Google Sheets to track tutor/helper attendance and make sure they are following their schedules, as well as providing insights to how many help sessions are being held.

2. **Session Calendar**

    Utilizes Google Calendar to display upcoming help sessions in the queue so that students can easily see when the next office hour session will happen.

### Ignoring Credentials in Git

For development, run this command in the `YABOB` directory:

``` bash
git update-index --skip-worktree ./src/environment/*.json 
```

This will ignore all changes in the credentials json files so they aren't accidentally pushed to the repository.

If the file structure changes in the future and requires new commits, run this:

``` bash
git update-index --no-skip-worktree ./src/environment/*.json 
```

and git will track these files again. Ignore the changes again after updates.

### Google Sheet Logging Extension Requirements

#### Service Account Key

1. Go to [Google Cloud](console.cloud.google.com) and add your google account of choice.

2. Click Select a Project on the top left and create a new project.

3. Then go to API & Services $\to$ *Credentials* tab $\to$ Create Credentials $\to$ Service Account.

4. Follow the guide to create a service account. Enable Owner Permission.

5. Once you are back at the *Credentials* tab, click on the account you just created $\to$ KEYS $\to$ Add Key: Create New Key $\to$ Choose JSON $\to$ It will prompt you to download your key.

    <img width="800" alt="Screen Shot 2022-09-01 at 12 10 18 AM" src="https://user-images.githubusercontent.com/60045212/188524386-11b98544-2af1-4d7a-9001-8a34b13363d5.png">

6. Copy the corresponding values of this JSON file into `YABOB/src/environment/development.json`.

   ```json
   "googleCloudCredentials": {
        "client_email": "",
        "private_key": ""
    }
   ```

   This `client_email` is the email of YABOB. We will share the google sheet with this email in the next step.

#### Google Sheets API

1. Still in the Google Cloud Console, go to APIs and Services $\to$ Enabled APIs & Services.

    <img width="800" alt="Screen Shot 2022-09-01 at 12 17 55 AM" src="https://user-images.githubusercontent.com/60045212/188524292-21dda450-af18-4ed7-9e61-14cd309b8dad.png">

2. Click on ENABLE APIS AND SERVICES.

3. Search for Google Sheets, Click on that then click ENABLE.

4. Search for Google Drive, enable this API as well.

Now we have access to google sheets, we just need the sheet itself.

#### Google Sheets ID

1. Create an empty Google Sheet and share the sheet to YABOB's service account email **with EDIT access**.

   - This is the `client_email` field under `googleCloudCredentials`
   <p></p>

2. The google sheets URL looks like this:

   ```
   https://docs.google.com/spreadsheets/d/<very long string here>/edit
   ```
   That very long string is your `YABOB_GOOGLE_SHEET_ID`.

3. Go to `YABOB/src/environment/development.json`. Fill in `YABOB_GOOGLE_SHEET_ID` with the string you just found.

    ``` json
    "googleSheetLogging": {
        "YABOB_GOOGLE_SHEET_ID": ""
    }
    ```

### Calendar Extension Requirements

#### Google Calendar API

1. Create a google calendar.

2. Go to that particular calendar's `Settings and Sharing` tab. Tick the **Make available to public** checkbox.

3. Scrolling down will take you do the **Integrate Calendar** Section. Copy the **Calendar ID**. It should end with `calendar.google.com`. This is your `YABOB_DEFAULT_CALENDAR_ID` and will be used as a fallback calendar

4. Go to [Google Cloud](console.cloud.google.com), then go to APIs and Services $\to$ Enabled APIs & Services

5. Enable the Calendar API

6. Still in APIs and Services, go to ***Credentials*** $\to$ CREATE CREDENTIALS: **API KEY**

7. Now you will be prompted with a long string. You can limit the scopes of this key by following the guide on screen. This is your `YABOB_GOOGLE_API_KEY`

8. Go to `YABOB/src/environment/development.json`, fill in

    ```json
    "sessionCalendar": {
        "YABOB_DEFAULT_CALENDAR_ID": "",
        "YABOB_GOOGLE_API_KEY": ""
    }
    ```

### Final Check

Everything in `YABOB/src/environment/development.json` should now have a value instead of an empty string.

## Step 5. Running with Extensions

Remove the `-- noExtension` flag from step 2.

```bash
npm run dev
```