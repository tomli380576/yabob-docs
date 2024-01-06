# Built-in Extension Commands

Updated Sept.24, 2023

# What are YABOB Extensions?

Since the beginning of YABOB v4.0, we switched over to a new implementation that separates base YABOB and YABOB extensions. This has enabled YABOB to have significantly better performance and allows us to develop new features more quickly.

YABOB extension commands do NOT affect base YABOB's behavior but rather listens for events happening in base YABOB and then shows different information in the #queue channel or in external resources.

### Format

The format in all of the following sections will be the same as the [Built in Commands](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Built-in-Commands) page with a new subtitle:

**Side Effects**: What will happen outside of discord.

## 📅 Session Calendar Extension

This extension reads from a google calendar you provide and manages the `Upcoming Sessions` embed inside queue channels to display upcoming office hours sessions. All of the following commands will affect what's being displayed in that embed.

### `/when_next`

Fetches from the calendar and displays upcoming office hour sessions.

+++Access Level

[!badge text="Student" variant="success" size="l"]

+++ Throws

Nothing.

+++ Example Usage

```
/when_next
```

+++

### `/make_calendar_string_all`

Generates a parsable calendar summary string for ALL the queues this user is approved for. This string needs to be put inside the calendar event's **description** section for the `Upcoming Session` embed in each `#queue` channel to recognize it.

+++ Options

`calendar_name` Specifies the user's name on the calendar. When this command is run, YABOB remembers the discord id of the user and maps it to `calendar_name`.

+++ Access Level

[!badge variant="danger" text="Bot Admin" size="l"]

[!badge variant="warning" text="Staff" size="l"]

+++ Throws

Nothing.

+++ Example Usage

```
/make_calendar_string_all calendar_name=John
```

This will return `YABOB_START John - class1, class2 YABOB_END` if John has the roles class 1 and class 2.

Suppose John's discord handle is `@cooljohn123`. If John made a google calendar event **titled** "Remote office hours for class 1 and class 2" and put "Remote" in the location field, the upcoming session embed will show:

![](https://user-images.githubusercontent.com/60045212/212770340-beb7c3d9-ac20-469f-ba88-780cd310e45e.png)

![](https://user-images.githubusercontent.com/60045212/212770423-9a0f5459-aad0-4f37-8f56-c157f8e4c9b1.png)

The timeStamp fields are [dynamic time strings in discord](https://gist.github.com/LeviSnoot/d9147767abeef2f770e9ddcd91eb85aa) where you can hover over it and see the literal date and time.
+++

### `/make_calendar_string`

Generates a calendar summary string that is guaranteed to be recognized by this extension. This string needs to be put inside the calendar event's **description** section for the `Upcoming Session` embed to recognize it.

- This command is meant to handle special cases when the staff member doesn't want to include all of their approved queues (Example: They want to host a final review session for only 1 class)
- For other use cases, we recommend using [`/make_calendar_string_all`](#make_calendar_string_all)

+++ Access Level

[!badge variant="danger" text="Bot Admin" size="l"]

[!badge variant="warning" text="Staff" size="l"]

+++ Options

`calendar_name` Specifies the user's name on the calendar. When this command is run, YABOB remembers the discord id of the user and maps it to `calendar_name`

`queue_name_1` `queue_name_2` $\dots$ `queue_name_20` Specifies which queues to be included in this calendar string. The specified queues will then display the calendar event upon refresh.

+++ Throws

`CommandParseError`

- If the user does not have the Staff role or the Bot Admin role
- If any of the given queues is not a valid queue category

+++ Example Usage

```
/make_calendar_string John class1 class2 ...
```

will return `YABOB_START John - class1, class2 YABOB_END`

After putting this string inside the calendar events, you can press `Refresh Upcoming Sessions` to refresh the embed. Your name and the calendar title should now appear.

+++ Side Effect

A backup is triggered.

+++

### 🔘 Refresh Upcoming Sessions

<img width="228" alt="Screenshot 2023-01-09 at 10 10 15 PM" src="https://user-images.githubusercontent.com/60045212/211474708-db5c4143-7229-479d-895a-a4e6232abfda.png">

Allows the user to manually refresh the calendar embed for 1 queue.

+++ Access Level

[!badge text="Everyone" size="l"]

+++ Throws

`CalendarConnectionError`

- If the calendar refresh request timed out
- If the calendar is not accessible

+++

---

## 📊 Google Sheet Logging

This extension logs attendance hours and help session history to a google sheet you provide. For each server, this extension will create 2 google sheet worksheets:

`<Your Server Name> Attendance Logs`

Tracks attendance of each staff member with the following information

- **Helper Username** - The discord user name of the helper
- **Discord ID** - The discord id of the helper
- **Time In** - When the helper started helping by using `/start`
- **Time Out** - When the helper stopped helping by using `/stop`
- **Helped Students** - The names of the students that received help
- **Session Time (ms)** - How much time elapsed between `/start` and `/stop` in milliseconds.
- **Active Time (ms)** - How much time was the helper actually helping students in a voice channel. This is tracked by recording the time a student stayed in a voice channel.
- **Number of Students Helped** The total number of students helped, synced with **Helped Students**.

`<Your Server Name> Help Session Logs`

Tracks how many students were being helped on your server.

- Student Username
- Student Discord ID
- Helper Username
- Helper Discord ID
- **Session Start** - When did the student join the voice channel.
- **Session End** - When did the student leave the voice channel
- **Wait Start** - When did the student started waiting.
- **Queue Name** - Which queue did the student get help from.
- **Wait Time (ms)** - How long did the student wait before getting dequeued.
- **Session Time (ms)** - How long were the student in the voice channel getting help.

### `/stats`

This command has 2 subcommands:

`/stats helper`

Returns an embed showing the statistic of a helper. The stats include:

- Help sessions
- Total available time
- Total helping time
- Number of student sessions
- Unique students helped
- Returning students
- Average session time
- Average wait time

`/stats server`

Returns an embed showing the statistic of the entire server. The stats are the same as `/stats helper`.

+++ Options

`time_frame` Required Parameter. Specifies which time period should the stats come from: All Time, Past Month, or Past Week.

+++ Access Level

[!badge text="Everyone" size="l"]

+++ Throws

`Error` (This will be updated with a different error)

- If there's no `Help Session Logs` worksheet of your server.
