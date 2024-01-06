# Built-in Commands

Updated on Sep.24 2023

This page covers all the commands of base YABOB. For extension commands, see the [extension commands page](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Built-in-Extension-Commands).


!!! **Where did some of the `/set_...` commands go?**
They have been moved under `/settings`
!!!


!!!info **The following sections will describe each command/button with this format**

<p></p>

**Title**: Command name.

- Matches the exact command name in discord

**Options**: Possible arguments of this command.

- Arguments with a question mark are **optional**

**Access Level**: The lowest required access level role to access the command.

- The roles are ordered (From high to low): Bot Admin > Staff > Student
- Example: If the access level specifies `Student`, then Bot Admin and Staff also has access to this command. But if the access level is `Bot Admin`, then Staff and Student does not have access.

- If you have the [administrator permission](https://discord.com/moderation/1500000176222-201-permissions-on-discord#title-2) of a server, you are not required to have any access level roles.

**Throws**: Possible errors that YABOB can throw.

- These errors are expected to happen, and YABOB will reply back to the user with the error. YABOB will not modify any internal states if it rejects the command.

!!!

## Command List

### `/enqueue`

Adds the user to the back of the queue.

#### Options

`queue_name?` - specifies which queue to join. If not specified, the user will be enqueued to the queue where they used the command if a `#queue` channel exists in its parent category.

#### Access Level

Student and above.

#### Throws

`CommandParseError`

- If the specified `queue_name` argument is not a valid queue channel.
- If the command is used without specifying `queue_name`, and it's used in a channel with no parent category or the category does not have a `#queue` channel.
- If the user does not have the student role.

`QueueError`

- If the student is already in the queue.
- If the queue is not open (ie. if the queue is closed or paused)

#### Example Usage

```bash
/enqueue ECS32A
/enqueue # left blank, YABOB will check if the current category has a queue channel
```

### `/leave`

Removes the user from a queue. If the `queue_name` argument is not specified, the user will be removed from the queue where they used the command if a `#queue` channel exists in its parent category.

#### Options

`queue_name?` - specifies which queue to leave from.

#### Access Level

Student.

#### Throws

`CommandParseError`

- If the specified `queue_name` argument is not a valid queue channel.
- If the command is used in a channel with no parent category or the category does not have a `#queue` channel.

`QueueError`

- If the student is not in the queue.

#### Example Usage

```bash
/leave ECS32A
/leave # left blank, YABOB will check if the current category has a queue channel
```

### `/list_helpers`

Lists all the helpers that are currently helping

#### Access Level

Student.

#### Throws

Nothing.

#### Example Usage

```bash
/list_helpers
```

### `/start`

Opens all the queues that the user has a role for and start helping students.

##### Options

`mute_notif?` - If true, opening the queue will NOT notify the students who have signed up for notifications and the notification group will not be cleared. Defaults to false.

#### Access Level

Staff or Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.

`ServerError`

- If the helper is already helping.
- If the helper does not have any class roles.

#### Example Usage

```bash
/start # notifies everyone in the notif group
/start mute_notif=True # none of the students will be notified
```

### `/stop`

Closes the office-hour queues and stop students from entering the queue.

- Students that were in the queue before closing will remain in the queue for the next office hour session.

- If Queue Auto Clear is enabled when the queue is closed, a timer will be started if there are students remaining in the queue.

#### Access Level

Staff or Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.

`ServerError`

- If the helper is not currently helping.

#### Example Usage

```bash
/stop
```

### `/next`

Invites the student at the front of the queue to the helper's voice channel.

#### Options

`queue_name?` - Specifies which queue to dequeue from. If not specified, dequeues the student that has been waiting the longest out of all the queues that the helper has a role for.

`user?` - Specifies which student to dequeue. If specified, YABOB will ignore queue order and search for this student. If `queue_name` is also specified, YABOB will search in that queue for this student.

#### Access Level

Staff and Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.
- If the specified `queue_name` is not a valid queue category

`ServerError`

- If there's no student to dequeue.
- If the specified `user` is not found in any of the queues.
- If the specified `user` is not found in the specified `queue_name`.

#### Example Usage

```bash
/next # dequeues globally
/next queue_name=ECS32A # dequeues from the queue named "ECS32A"
/next user=someStudent # searches for the user with the username "someStudent"
/next queue_name=ECS32A user=someStudent # searches for the user with the username "someStudent" in the queue named "ECS 32A"
```

### `/announce`

Sends a message to all of the students in all the queues that the helper is approved to help for.

#### Options

`message` - Required. The announcement message content.

`queue_name?` - Which queue to send the announcement to. If not specified, YABOB will send the message to all the queues that the helper has a role of.

#### Access Level

Staff or Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.
- If the specified queue category does not have a `#queue` channel.
- If the announcement is longer than 4096 characters. This limit comes from discord's API.
- If the specified `queue_name` is not a valid queue category.

#### Example Usage

```bash
/announce "We are having pizza tonight!"
/announce "We are having pizza tonight!" ECS32A
```

### `/pause`

Marks the user as a helper who has temporarily stopped accepting new students. When new students join the queue, this helper will not be notified.

<div align='center'>
<img alt="pause command" src="https://user-images.githubusercontent.com/60045212/211220914-9a5de922-f5a0-48da-80b0-a8280f247dc3.gif">
</div>

#### Access Level

Staff, Bot Admin

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.

`ServerError`

- If the user is not currently helping.
- If the user is already a paused helper.

#### Note: `PAUSED` queues

If all the helpers of the same queue have used `/pause`, this queue will enter the `PAUSED` state that will stop students from joining the queue. Helpers of a `PAUSED` queue can still dequeue students. As of v4.3, ALL helpers of the same queue must use `/pause` to make the queue `PAUSED`.

- The queue embed of a `PAUSED` queue will have a yellow embed instead of an aqua embed.
  <div align='center'>
  <img width="400" alt="Screenshot 2023-01-08 at 5 07 58 PM" src="https://user-images.githubusercontent.com/60045212/211227978-934bda64-3485-4933-80a7-5d2810a73db5.png">

  </div>

### `/resume`

Lets a helper exit the `paused` state. They will become `active` if the command succeeds.

#### Access Level

Staff, Bot Admin

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.

`ServerError`

- If the user is not currently helping
- If the user is not a `paused` helper

### `/clear`

Clears the specified queue.

#### Options

`<queue_name>` - Required parameter. Specifies which queue to clear.

#### Access Level

Staff or Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Staff or Bot Admin role.
- If the specified queue category does not have a `#queue` channel.

`ServerError`

- If there is no student in the specified queues

#### Example Usage

```bash
/clear ECS32A # Clears the queue named "ECS32A"
```

### `/clear_all`

Clears all the queues on this server.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the specified queue category does not have a `#queue` channel.

`ServerError`

- If there is no student in any of the queues

#### Example Usage

```bash
/clear_all
```

### `/cleanup_queue`

Debug feature. If the embeds in a queue were accidentally deleted, this command will ask the queue to perform a clean render.

#### Options

`<queue_name>` - Required Parameter. Which queue to clean up.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the specified queue category does not have a `#queue` channel.

#### Example Usage

```bash
/cleanup_queue ECS32A # sends new embeds in the #queue channel in category "ECS32A"
```

### `/cleanup_all`

Same as `/cleanup_queue` but affects all the queues.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the specified queue category does not have a `#queue` channel.

### `/cleanup_help_channels`

Debug feature. If the embeds in a the help channels were accidentally deleted, this command will re-send all the help channel embeds.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.

#### Example Usage

```bash
/cleanup_help_channels
```

### `/queue`

This command has 2 sub-commands.

`/queue add <queue_name>`

- Creates a new category with the name entered in `queue_name` and creates the #queue and #chat text channels within it.

`/queue remove <queue_name>`

- Deletes an existing category with the name entered in `queue_name` and all the channels within it.

#### Options

`queue_name` - Required Parameter

- For `/queue add`, this is a string of the name of the new queue
- For `/queue remove`, this is the name of the existing queue

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If a queue with the same name already exists when using `/queue add`
- If `/queue remove` was invoked in the queue category that the user wants to delete. YABOB will prompt the user to use the command in another text channel.

#### Example Usage

```bash
/queue add ECS32A
/queue remove ECS32B
```

### `/set_logging_channel`

Sets the logging channel on this server. YABOB will start sending log messages to this text channel.

#### Options

`channel` - Required Parameter. Which text channel to send logs to.

#### Access Level

Bot Admin

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the specified channel is being used as a queue channel.
- If the specified channel is not a text channel.

### `/stop_logging`

Stops YABOB from sending log messages to this server.

#### Access Level

Bot Admin

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.

### `/set_roles`

Sets the access level roles on this server. Namely, controls which roles should be interpreted as `Bot Admin`, `Staff`, and `Student`.

#### Options

`role_name` - Which access level role you want to change. `Bot Admin`, `Staff`, or `Student`.

`role` - The role to change to.

#### Access Level

Bot Admin

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the specified role is a bot integration role. These roles can bon

#### Example Usage

```bash
/set_role "Bot Admin" @Administrator # now YABOB will treat @Administrator as the Bot Admin role
```

### `/prompt_help_topic`

This command has 2 subcommands.

`/prompt_help_topic on`

- YABOB will start showing students a modal that asks them what topic do they need help with. Students are not required to submit the modal, and they will still be queued if they don't submit.

- This modal is shown when the student uses `/enqueue` or `[Join]`

<div align='center'>
<img width="400" alt="Screenshot 2023-01-08 at 5 03 13 PM" src="https://user-images.githubusercontent.com/60045212/211227818-bbeed230-c9bc-439a-8a31-f72d2c9498de.png">
</div>

Once the student submits this modal, all helpers of this queue will see:

<div align='center'>
<img width="400" alt="Screenshot 2023-01-08 at 5 05 10 PM" src="https://user-images.githubusercontent.com/60045212/211227887-0e9f0e6f-0a52-4220-a7a2-4a75a06c0907.png">
</div>

`/prompt_help_topic off`

- YABOB will not show students the Help Topic modal. Students will be directly enqueued when they use `/enqueue` or the `[Join]` button.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.

### `/settings`

Brings up the settings menu. See more details in the [Configuration Guide](https://github.com/KaoushikMurugan/yet-another-better-office-hour-bot/wiki/Configure-YABOB-Settings-For-Your-Server).

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.

### `/create_offices`

Batch creates voice channels with pre-configured permissions. As of 4.3, voice channels created with this command only allows Bot Admin and Staff to have direct access to the channels. Students can see and join the channels within 15 minutes after they are dequeued.

#### Options

All 3 options are required.

`category_name`: The name of the category that holds all the voice channels.

`office_name`: The prefix of each voice channel.

`number_of_offices`: How many channels to create

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.
- If the inputs of `category_name` or `office_name` are not valid discord channel names.
- If the input of `number_of_offices` is not a valid integer

#### Example

```bash
/create_offices "Offices Hour Voice Channels" "office" 5
```

Running this command creates the following:

<div align='center'>
<img width="400" alt="Screenshot 2023-01-06 at 7 37 57 PM" src="https://user-images.githubusercontent.com/60045212/211129591-fdc19d53-d493-4fa1-9ba4-bb22fd5665e5.png">
</div>

### `/help`

Get help with a command.

#### Options

`command` - Required Parameter. The name of the command to get help with. Currently, we are hitting the upper limit of this option so some of the commands are not listed. v4.3.1 will add auto complete to `/help` to display all the commands.

#### Access Level

Student and above.

#### Throws

Nothing.

### `/auto_give_student_role`

This command has 2 sub commands.

`/auto_give_student_role on`

- YABOB will automatically give new members the `@Student` access level role. The role will be given as soon as new members join the server.
- If the `@everyone` role is the students role, this setting is ignored.

`/auto_give_student_role off`

- YABOB will not automatically give new members the `@Student` access level role. Students need to be manually assigned this role.

#### Access Level

Bot Admin.

#### Throws

`CommandParseError`

- If the user does not have the Bot Admin role.

## Button List

This section covers these buttons:

<div align='center'>
<img width="400" alt="Screenshot 2023-01-08 at 5 14 03 PM" src="https://user-images.githubusercontent.com/60045212/211228232-35a36115-6d08-4dc8-96d2-2540b7103fc1.png">

</div>

### 🔘 Join

Adds the user to the end of the queue on button click.

#### Access Level

Anyone.

#### Throws

`CommandParseError`

- If the `#queue` channel does not have a parent category.

`QueueError`

- If the student is already in the queue.

### 🔘 Leave

Removes the user from the queue on button click.

#### Access Level

Anyone.

#### Throws

`CommandParseError`

- If the `#queue` channel does not have a parent category.

`QueueError`

- If the student not in the queue.

### 🔘 Notify When Open

Adds the user to the notification group for that queue. The user will be automatically removed from the notification group once the queue is open.

#### Access Level

Anyone.

#### Throws

`CommandParseError`

- If the `#queue` channel does not have a parent category.

`QueueError`

- If the user is already in the notification group.

### 🔘 Remove Notifications

Removes the user from the notification group for that queue.

#### Access Level

Anyone.

#### Throws

`CommandParseError`

- If the `#queue` channel does not have a parent category.

`QueueError`

- If the user is not in the notification group.
