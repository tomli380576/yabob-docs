---
icon: sparkle-fill
---

# Invite Procedure

!!!warning

This invite procedure applies to YABOB v4.3 and above.

!!!

---

## Invite Link

[!button variant="success" text="Invite YABOB to Your Server"](https://discord.com/api/oauth2/authorize?client_id=967586305959657482&permissions=8&scope=bot)

## Video Guide

[!embed](https://www.youtube.com/watch?v=e7CpJED-c7w)

## Text Guide

1.  Create a discord server
2.  Use the [invite link](https://discord.com/oauth2/authorize?client_id=967586305959657482&permissions=8&scope=bot) to invite YABOB to your server
3.  If other roles exist on your server, YABOB will send you a direct message asking you to give it the highest role. The message will look like:

    ![](https://user-images.githubusercontent.com/60045212/211128697-ccf287c9-8f75-48fc-a856-ca5cb498e87a.png)

    Go to Server Settings $\to$ Roles and drag the `YABOB` role all the way to the top. Save the setting.

    Now YABOB will send you another direct message saying that it has gotten the highest role and is initializing for your server.

    ![](https://user-images.githubusercontent.com/60045212/211128723-c208ccf5-76f3-4620-ab22-b44d1cae996d.png)

4.  YABOB will now ask you to set up the access level roles for your server. If you don't have any roles in the server, you can use either the `[Create New Roles]` or `[Create New Roles (@everyone is student)]` option for quick setup. You can see the full documentation [here](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Configure-YABOB-Settings-For-Your-Server#server-roles).

    ![](https://user-images.githubusercontent.com/60045212/211129036-5946fe37-5cf6-41f2-a4e7-a0ef845dd6ea.png)

    After you make a choice, YABOB will save your settings and update the message:

    ![](https://user-images.githubusercontent.com/60045212/211128775-4faa109b-fddd-4a30-a688-2c9bd09a43bb.png)

5.  Now you are ready to create queues! In your server, use [`/queue add`](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Built-in-Commands#queue-add--remove-queue_name) to create some new queues.

6.  (Optional) You can batch create office hour voice channels easily with [`/create_offices`](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Built-in-Commands#create_offices-category_name-office_name-number_of_offices). YABOB will configure the voice channel permissions for you based on the roles you selected in step 4.

7.  (Optional) You can further configure YABOB settings with the `/settings` command. Learn more about YABOB settings [here](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Configure-YABOB-Settings-For-Your-Server).

8.  (Optional) When you are no longer using a server for your office hours, simply delete the server itself or kick YABOB from that server.
