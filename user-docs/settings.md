---
icon: gear
---

# Configure YABOB Settings

Starting with version 4.3, YABOB will post a `/settings` command to your sever as the entry point for all server related configurations such as:

- [After session message](#after-session-message): The message sent to the students after they finish receiving help
- [Roles](#server-roles): Which roles should YABOB use as Bot Admin, Staff, and Student
- [Queue Auto Clear](#queue-auto-clear): How long should YABOB wait until all closed queues will be automatically cleared
- [Logging Channel](#logging-channel): Which text channel should YABOB send logs to
- [Auto Give Student Role](#auto-give-student-role): Whether to automatically give new members the @Student Role upon joining the server
- [Calendar Settings](#calendar-settings): Which calendar should YABOB read from for the Upcoming Sessions Embed
- [Google Sheet Settings](#google-sheet-settings): Which google sheet should YABOB write attendance logs to

!!!danger Note
1. All settings are only available to Bot Admins. 
2. All settings are enabled/disabled for all queues on the server.
   For example, if queue auto clear is set to 30 minutes, then ALL queues on the server will clear 30 minutes after they close.
!!!

## 📝 Server Roles

YABOB needs to know which roles to interpret at [!badge variant="danger" text="Bot Admin"], [!badge variant="warning" text="Staff"], and [!badge variant="success" text="Student"] to enforce the access levels of all the commands. This setting controls which role will be used. We recommend using either one of the "Use Existing Roles" option.

### 🔘 Use Existing Roles

Use existing roles named the same as the missing roles. If not found, create new roles.

### 🔘 Use Existing Roles (@everyone is Student)

Same as [Use Existing Roles](#use-existing-roles), but use the @everyone role for the Student role if missing

### 🔘 Create New Roles

Create brand new roles for the missing roles.

!!!warning
If roles named Bot Admin, Staff, or Student already exist, duplicate roles will be created.
!!!

### 🔘 Create New Roles (@everyone is Student)

Same as [Create New Roles](#create-new-roles), but use the @everyone role for the Student role if missing

### `/set_roles` Command

For more granular control, use the [`/set_roles`](/user-docs/built-in-commands.md#set_roles) command to individually configure the roles.

---

## ⏳ Queue Auto Clear

This setting controls whether the queues will automatically clear themselves after closing.

### 🔘 Set Auto Clear Time

Use the modal to put in the number of hours and minutes to wait before clearing a closed queue. For example, if auto clear is set to `9h 0min`, then after the queue becomes closed, everyone that's still in the queue will be cleared in 9 hours.

- Maximum timeout is 100 hours and 39 minutes. (If 99 hours 99 minutes is entered into the textbox)
- If there's a running timer when this command is used, the old timer will be discarded and a new timer with the new timeout will be started.

Queue channels will also display the timer:

![](https://user-images.githubusercontent.com/60045212/208558810-367b51da-d425-486f-a216-8727901176bb.png)

---

## 🪵 Logging Channel

This setting controls which channel should YABOB send logging messages to. The logs will look like the following:

![](https://user-images.githubusercontent.com/60045212/208606091-8357e490-70d4-4abc-9da5-89cd6384bb6a.png)

All command usages, button presses, and errors will be sent to this channel.

## 📨 After Session Message

Sometimes it could be useful to automatically send a message to the students after they finish receiving help, such as asking for feedbacks. Here's an example message:

![](https://user-images.githubusercontent.com/60045212/209428485-94e515e2-9c21-415d-bb2c-387b536c42d8.png)

### 🔘 Set After Session Message

Use the modal to input the after session message. All discord markdown syntax is supported. You can check which markdown rules are supported on discord [here](https://gist.github.com/matthewzring/9f7bbfd102003963f9be7dbcf7d40e51)

---

## 🎓 Auto Give Student Role

Manually assigning the student role to new members could be tedious. This setting configures whether YABOB should automatically give the student role to new members when they join the server.

### 🔘 Enable

YABOB will automatically give new members the [!badge variant="success" text="Student"] role as soon as they join the server. You can always check which role is [!badge variant="success" text="Student"] on your server by going to the [Server Roles](#server-roles) setting.

### 🔘 Disable

YABOB will not add roles to new members.

---

## 📅 Calendar Settings

Changes the calendar this server reads from for office hour events.

### 🔘 Change Calendar Settings

Once clicked, a form will pop up asking for a calendar ID and an optional public embed URL.

#### How to find this calendar ID

1. Open Google Calendar, click the 3 dots on the calendar that you want to use $\to$ click `Settings and Sharing`.

2. Click `Make available to public`
    
    ![](https://user-images.githubusercontent.com/60045212/211473315-7196c6f0-4cbd-40bc-92d5-f69e82f8476d.png)

3. Scroll down to `Integrate Calendar` and copy the string under `Calendar ID`. This is the id string of your calendar
    
    ![](https://user-images.githubusercontent.com/60045212/211473831-ca1c94a4-87b6-4f80-88f8-d6aa790eff40.png)

4. Fill the calendar id field with this id.

If the public embed url is unspecified, the `[Full Calendar]` button in all the `#queue` channels will take users to the default google calendar embed, which looks like the following:

![](/static/default-calendar.png)

If you wish to have a different calendar website than the default google calendar embed, fill in the public embed URL as well (URL must be **complete**, i.e include https).

!!!warning
YABOB cannot check if this URL is safe for users to click on, so it is the server owner's responsibility to ensure the link is safe.
!!!

---

## 🙋 Help Topic Prompt

Controls whether to show students a modal asking for a help topic. This is useful if helpers wishes to know what the students in the queue need help with before they join the voice channel.

### 🔘 Enable

YABOB will start showing students a modal that asks them what topic do they need help with. **Students are not required to submit the modal (they can dismiss it by closing it or clicking Dismiss)**, and they will still be queued if they don't submit.

This modal is shown when the student uses `/enqueue` or click `[Join]`.

![](https://user-images.githubusercontent.com/60045212/211227818-bbeed230-c9bc-439a-8a31-f72d2c9498de.png)

Once the student submits this modal, all helpers of this queue will see:

![](https://user-images.githubusercontent.com/60045212/211227887-0e9f0e6f-0a52-4220-a7a2-4a75a06c0907.png)

### 🔘 Disable

YABOB will not show students the Help Topic modal. Students will be directly enqueued when they use `/enqueue` or the `[Join]` button.

---

## 🧐 Serious Mode

Controls whether to show "fun stuff" like emojis in queue channels.

### 🔘 Enable

Removes all emotions and the hidden cat from the queues

![](https://user-images.githubusercontent.com/60045212/211228074-910aefb5-d8b9-40c6-98d3-cf944a01d2d4.png)

### 🔘 Disable

Enables all emotions and the hidden cat from the queues

![](https://user-images.githubusercontent.com/60045212/211228094-98d62e9c-8a5a-4926-bcf6-ccbaa65cab51.png)

---

## 📊 Google Sheet Settings

Controls which Google Sheet this server will be used for logging.

![](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/assets/60045212/68bfa8da-dc3f-4d60-ab02-401b59012445)

### 🔘 Change Google Sheet

Use this button to change which google sheet to use for logging helper hours. Clicking this button will prompt a modal asking for a google sheet ID.

#### How to find this google sheet id

A google sheet's URL looks like this:

```
https://docs.google.com/spreadsheets/d/<thisVeryLongStringIsYourGoogleSheetIdYesItsReallyLong>/edit
```

The string after `d/` and before `/edit` is your google sheet id.

#### Before you click submit

Make sure the google sheet is shared with the YABOB in your server and YABOB has **EDIT** permission. You can find YABOB's email in the google sheet settings menu.

You can always revoke the permission by removing yabob from the list of accounts with access or reset to the default google sheet.

+++ Throws

`GoogleSheetConnectionError`

- If this google sheet is not accessible. This can happen if you did not share the google sheet with the YABOB inside your server. Don't worry, YABOB will tell you what its email address is when this error happens.

+++ Side Effect

No immediate side effect. The next log will be written to this new google sheet.

+++

### 🔘 Reset Google Sheet

Clicking this button will tell YABOB to use the default (public) google sheet for logging tutor hours. We recommend not using this default sheet for production environment since it's public.
