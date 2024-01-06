---
order: 99
icon: mortar-board
---

# How To Host & Join Office Hours with YABOB

Updated Jan.6, 2024

This page assumes that you have already invited YABOB to your server and created at least 1 queue. If not, please refer to the [Invite Procedure Page](invite-procedure.md) to get started.

## Access Level Roles

If you are not familiar with Discord roles, check out this [official explanation](https://support.discord.com/hc/en-us/articles/214836687-Role-Management-101).

YABOB interacts with 3 roles on your server.


[!badge variant="danger" text="Bot Admin"]
:   Members with total control over YABOB. The default bot admin role created during server initialization is: <img width="70" height='20' alt="Screenshot 2023-01-13 at 4 00 01 PM" src="https://user-images.githubusercontent.com/60045212/212439297-741d077c-7067-49a4-ae4c-c931e3ab569f.png">

[!badge variant="warning" text="Staff"]
:   Members that can host office hours and open queues. The default staff role created during server initialization is: <img width="45" height='20' alt="Screenshot 2023-01-13 at 4 00 39 PM" src="https://user-images.githubusercontent.com/60045212/212439348-3dc782be-8158-44ec-b0f3-98bdb160b027.png">

[!badge variant="success" text="Student"]
:   Members that can join queues and get help. The default student role created during server initialization is: <img width="55" height='20' alt="Screenshot 2023-01-13 at 4 01 19 PM" src="https://user-images.githubusercontent.com/60045212/212439398-559f132e-9128-4e07-a1bf-b993b7232dba.png"> 

These roles are treated as access level roles and used to restrict members of your server from accessing certain commands. For example, [`/clear_all`](built-in-commands.md#clear_all) could be a disastrous command if everyone has access to it.

To get started, we recommend:

-   Giving [!badge variant="danger" text="Bot Admin"] to the administration team of the server. This could be the professor(s) of a course, the managers of a tutoring service etc.

-   Giving [!badge variant="warning" text="Staff"] to the teaching assistants / tutors / staff members.

-   Giving [!badge variant="success" text="Student"] to everyone else or members that have verified their identity. (Example: Some servers require students to have a valid school email.)

## Office Hour Protocol

Before starting office hours, staff members need to be given queue roles of the queues they are approved to help for. These roles are automatically created when you use `/queue add <queue_name>` and have the same name as the queue category. For example, if your queue is called "Class A", then the queue role is also called "Class A".

A typical office hour session goes like this:

1. [!badge variant="warning" text="Staff"] opens queues with [`/start`](built-in-commands.md#start) and joins a voice channel.

2. [!badge variant="success" text="Student"] joins the queues they need help with by pressing the [`[Join]`](built-in-commands.md#join) button or using the [`/enqueue`](built-in-commands.md#enqueue) command.

    [!badge variant="warning" text="Staff"] will receive a direct message from YABOB saying that a student has joined a queue.

3. [!badge variant="warning" text="Staff"] uses [`/next`](/user-docs/built-in-commands.md#next) to dequeue a student.

    [!badge variant="success" text="Student"] who was dequeued receives a direct message from YABOB saying that it's their turn to join the office hour voice channel. This message also contains a link to that voice channel.

4. [!badge variant="success" text="Student"] joins the voice channel and get help.

5. [!badge variant="success" text="Student"] leaves the voice channel after they are done receiving help.

6. [!badge variant="warning" text="Staff"] uses [`/stop`](/user-docs/built-in-commands.md#stop) to close the queues.

If you are not familiar with discord voice channel features, [check out this official guide](https://support.discord.com/hc/en-us/articles/360045138571-Beginner-s-Guide-to-Discord##h_93cf0203-d5bb-4e02-b1b2-80dad445895d).

## Advanced Usage (Staff & Bot Admin)

In addition to the protocol above, there are also more in-depth commands you can use to fully control your office hours:

1. You can use [`/pause`](built-in-commands.md#pause) and [`/resume`](built-in-commands.md#resume) to temporarily stop accepting new students.

2. You can ask students to submit a short description of what they need help with when they join the queue. This can be done with [`/prompt_help_topic on`](built-in-commands.md#prompt_help_topic) or inside the [settings menu](built-in-commands.md#settings).

3. You can track staff member's attendance and conduct analytics with the [Google Sheets Logging Extension](extension-commands.md#google-sheet-logging).

4. You can require students to be verified through some method before they are allowed to join queues. For example, you can invite another email verification bot that automatically gives new members the [!badge variant="success" text="Student"] access level role when they verify their email.

5. Let YABOB automatically send a message to students after they leave the voice channel with the [after session message](/user-docs/settings.md#after-session-message) setting.

## Advanced Usage (Students)

1. Although the voice channels are hidden from you, the voice channel status is still visible inside `#queue` channels. If a staff member is busy helping students, you will see that they are "Busy in `<Voice Channel Name>`"

2. You can see when there will be more office hour sessions directly inside ##queue channels with the "Upcoming Sessions" embed from the Google Calendar Extension.

3. (If [!badge variant="danger" text="Bot Admin"] completed the setup) You can use [`/when_next`](extension-commands.md#when_next) to ask YABOB to fetch from the linked google calendar and list upcoming office hour sessions.

4. Look for the cat in queues! It's very mysterious and only has <10% chance of showing up.

5. See what YABOB is doing by checking its discord presence!

There are many more commands and settings you can use beyond what we have described here in the [Built-in Commands](built-in-commands.md) and [Extension Commands](extension-commands.md) page. Happy hosting office hours!
