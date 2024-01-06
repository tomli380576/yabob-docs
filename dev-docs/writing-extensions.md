---
icon: project-template
order: 9
---

# Writing Extensions

Updated on Jan. 10, 2022

This page applies to YABOB v4.3 and above

## So you want to build your own extensions...

That's great! YABOB V4 is designed to be customizable and extensible. So we provided the following 3 levels of extensions interfaces.

1. Interaction Level
2. Server Level
3. Queue Level

Server and Queue extensions will be able to get data from the base YABOB using the [Observer Pattern](https://refactoring.guru/design-patterns/observer). Interaction level extension can add custom commands/buttons allowing users to interact with your extensions.

The 3 levels **never directly interact with each other** by design. If your extension spreads across all 3 levels, you should also create a shared state class.

Everything in the following interfaces are optional. Make sure your extension inherit from the concrete base classes described below. (unless your extension implements the entire interface)

### Parameters

The `FrozenServer`, `FrozenQueue`, `FrozenDisplay` interfaces removes all the methods that should not be accessed and marks everything else readonly.

## Server Level Extensions

These extensions are loaded when an instance of `AttendingServerV2` is created.

```ts
interface ServerExtension {
    onServerInitSuccess: (server: FrozenServer) => Promise<void>;
    onAllQueuesInit: (
        server: FrozenServer,
        allQueues: ReadonlyArray<HelpQueueV2>
    ) => Promise<void>;
    onDequeueFirst: (
        server: FrozenServer,
        dequeuedStudent: Readonly<Helpee>
    ) => Promise<void>;
    onHelperStartHelping: (
        server: FrozenServer,
        helper: Readonly<Omit<Helper, "helpEnd">>
    ) => Promise<void>;
    onHelperStopHelping: (
        server: FrozenServer,
        helper: Readonly<Required<Helper>>
    ) => Promise<void>;
    onStudentJoinVC: (
        server: FrozenServer,
        studentMember: GuildMember,
        voiceChannel: VoiceChannel
    ) => Promise<void>;
    onStudentLeaveVC: (
        server: FrozenServer,
        studentMember: GuildMember
    ) => Promise<void>;
    onServerDelete: (server: FrozenServer) => Promise<void>;
    loadExternalServerData: (
        serverId: string
    ) => Promise<Optional<ServerBackup>>;
    onServerRequestBackup: (server: FrozenServer) => Promise<void>;
}
```

!!!danger
Your extension need to inherit from the concrete `BaseServerExtension` class
!!!

### Loading Server Extensions

In the `AttendingServerV2.create()` method, find this line:

```ts
const serverExtensions = disableExtensions
    ? []
    : await Promise.all([
          AttendanceExtension.load(guild.name),
          FirebaseLoggingExtension.load(guild.name, guild.id),
      ]);
```

Then add the constructor / async create method.

```ts
const serverExtensions = disableExtensions
    ? []
    : await Promise.all([
          AttendanceExtension.load(guild.name),
          FirebaseLoggingExtension.load(guild.name, guild.id),
          MyAsyncExtension.load(/*... some parameters*/),
          new MySyncExtension(/*... some parameters*/),
      ]);
```

### Server Events

#### `onServerInitSuccess`

Called when an `AttendingServerV2` is successfully created.

#### `onAllQueueInit`

Called when all the queues inside the server are done rendering and ready to go.

#### `onQueueDelete`

When a queue gets deleted using the `/queue remove (queue_name)` command

#### `onDequeueFirst`

When a student is dequeued by a helper using the `/next` command.

#### `onHelperStartHelping`

When a helper uses `/start`

#### `onHelperStopHelping`

When a helper uses `/stop`

#### `loadExternalServerData`

Called on inside `AttendingServerV2.create()`. Currently, the server will load the first backup provided by any extension. You can also alter this behavior by changing how this function is called inside `AttendingServerV2.create()`.

#### `onStudentJoinVC`

Called when a student joins VC through the invite sent after a helper uses `/next`. A voice channel object is guaranteed to exist.

#### `onStudentLeaveVC`

Called when a student leaves VC after joining with the invite sent after a helper uses `/next`.

#### `onServerDelete`

Called when YABOB is kicked from a server. If your extension maintains an internal state in memory, this is the time to run any necessary cleanup.

---

## Queue Level Extensions

These extensions are loaded when an instance of `HelpQueueV2` is created.

```ts
interface QueueExtension {
    onQueueCreate: (queue: FrozenQueue) => Promise<void>;
    onQueueOpen: (queue: FrozenQueue) => Promise<void>;
    onQueueClose: (queue: FrozenQueue) => Promise<void>;
    onEnqueue: (queue: FrozenQueue, student: Readonly<Helpee>) => Promise<void>;
    onDequeue: (queue: FrozenQueue, student: Readonly<Helpee>) => Promise<void>;
    onStudentRemove: (
        queue: FrozenQueue,
        student: Readonly<Helpee>
    ) => Promise<void>;
    onRemoveAllStudents: (
        queue: FrozenQueue,
        students: ReadonlyArray<Helpee>
    ) => Promise<void>;
    onQueueRender: (
        queue: FrozenQueue,
        display: FrozenDisplay
    ) => Promise<void>;
    onQueueDelete: (deletedQueue: FrozenQueue) => Promise<void>;
}
```

!!!danger
Your extension need to inherit from the concrete `BaseQueueExtension` class
!!!

### Loading Queue Extensions

In the `HelpQueueV2.create()` method, find this line:

```ts
const queueExtensions = disableExtensions
    ? []
    : await Promise.all([
          CalendarQueueExtension.load(1, queueChannel.queueName),
      ]);
```

Then add the constructor / async create method.

```ts
const queueExtensions = disableExtensions
    ? []
    : await Promise.all([
        CalendarQueueExtension.load(
                1, // This will be the 1st non-queue embed
                queueChannel.queueName
            ),
        MyAsyncQueueExtension.load(/*params*/, 2), // <- 2 is the renderIndex. This will be the 2nd non-queue embed
        new MySyncQueueExtension(/*params*/, 3) // <- 3 is the renderIndex. This will be the 3rd non-queue embed
      ]);
```

### Sending your own embeds

If you would like your extension to have a custom embed inside a #queue channel, make sure to:

1. Accepts a readonly **`renderIndex`** in the extension's constructor or asynchronous create method.

    - This is assigned to each extension during queue create. With this index, `QueueDisplayV2` will help make sure that all embeds are correctly rendered.
    - If any embed gets accidentally deleted and a queue render was triggered, `QueueDisplayV2` will try to re-render the queue embed and extension embeds using the rendering method your extensions provide.
    <p></p>
2. If your embed will re-render WITH the queue, override the `onQueueRender` method and call `display.requestNonQueueEmbedRender` to send your embed

3. If your embed will NOT render with the queue, accept the queue display as a parameter in the extension constructor or the async create method and hold on to the display object and mark it as `readonly`. Then when you are ready to send the embeds, call `display.requestNonQueueEmbedRender`

### Queue Events

#### `onQueueCreate`

Called when a queue is successfully created, meaning all renderings are complete.

#### `onQueueOpen`

Called when a queue is successfully opened.

#### `onQueueClose`

Called when a queue is successfully closed.

#### `onEnqueue`

When a student joins the queue.

#### `onDequeue`

When the 1st student is dequeued.

#### `onStudentRemove`

When a students is removed from the queue. Happens when a student chooses to leave the queue using `/leave` or the Leave Button.

#### `onRemoveAllStudents`

When everyone in the queue is removed. Happens when a helper uses `/clear (queue)`. This will not trigger onStudentRemove().

#### `onQueueRender`

Called when the queue embed starts to re-render.

#### `onQueueDelete`

Called when a queue is using `gracefulDelete`, which is triggered by /queue remove (as of right now). If your queue extension maintains an internal state in memory, this is the time to run any necessary clean up procedure.

---

## Interaction Level Extensions

This is a special type of extension. Loaded in the `joinGuild` method in `app.ts`.

```ts
interface InteractionExtension {
    /**
     * Do an initialization check at YABOB instance level
     * - Called inside client.on('ready')
     * - Errors thrown here will NOT be caught
     */
    initializationCheck(): Promise<void>;
    /**
     * The command data json to post to the discord server
     */
    slashCommandData: CommandData;
    /**
     * Help messages to be combined with base yabob help messages
     */
    helpMessages: {
        botAdmin: ReadonlyArray<HelpMessage>;
        staff: ReadonlyArray<HelpMessage>;
        student: ReadonlyArray<HelpMessage>;
    };
    /**
     * These options appear in the select menu of the main menu of /settings
     */
    settingsMainMenuOptions: ReadonlyArray<SettingsMenuOption>;
    /**
     * Command method map
     */
    commandMap: CommandHandlerProps;
    /**
     * Button method map
     */
    buttonMap: ButtonHandlerProps;
    /**
     * Select menu method map
     */
    selectMenuMap: SelectMenuHandlerProps;
    /**
     * Modal submit method map
     */
    modalMap: ModalSubmitHandlerProps;
}
```

!!!danger
Your extension need to inherit from the concrete `BaseInteractionExtension` class
!!!


### Loading Interaction Extensions

In `interaction-entry-point.ts`, find this line:

```ts
const interactionExtensions: ReadonlyArray<InteractionExtension> =
    environment.disableExtensions
        ? []
        : [
              new SessionCalendarInteractionExtension(),
              new GoogleSheetInteractionExtension(),
          ];
```

Then add your extension in the array. The creation method must be through a constructor.

### Method Maps

Your extension need to maintain at least 1 of the following:

-   `commandMap`
-   `buttonMap`
-   `selectMenuMap`
-   `modalMap`

where the keys are the names of your command's `commandName` or the names that are encoded into `setCustomId`. The values are handler functions that will respond to the interaction. Your method maps will be combined with the base YABOB's method map once YABOB starts.

### `slashCommandData`

These are the objects from `SlashCommandBuilder.toJSON()`. On start up, `app.ts` will post your commands along with all the built in commands to your discord servers. Make sure your command names does NOT overlap with any of the built in ones, otherwise Discord API will refuse to post any command.

The built in ones can be found in the [Built-in Commands](/user-docs/built-in-commands.md) page.

## Accessing External Data in Extensions

Extensions at each level are launched with **`Promise.all()`** meaning that extensions that listen to the same event will be started synchronously and YABOB will wait for the last one to finish. To avoid race conditions, we recommend extensions that listen to the same event to NOT WRITE TO the SAME data source.

## Extensions with Multiple Levels, Managing Internal State

Since the 3 levels of extensions don't directly interact with each other, you will need a state object that acts as a mediator

![session-calendar_diagram](https://user-images.githubusercontent.com/60045212/191892425-04d96295-1247-4efa-ab36-4c553bf6cc8d.png)

-   When the queue level extensions are loaded, they will push themselves into the `serverIdCalendarStateMap.listeners` hashmap.

-   The command level extension is only responsible for processing the commands, then write to `calendarExtensionConfig` or update the `calendarNameDiscordIdMap`. After the command extension is done updating the state, it will call `onCalendarExtensionStateChange()` for each listener in the `listeners` map. This is a catch-all event for this particular extension, but you can always add more sophisticated ones!
-   The queue level extension is only responsible for grabbing events based on the calendar id in `calendar-config.json` then producing the embed based on the calendar title and the contents in `calendarNameDiscordIdMap`.
-   The `CalendarServerEventListener` class listens for the server delete event and cleans up the `serverIdCalendarStateMap` for this server.

These 2 levels are loosely coupled with each other. Command level will not directly affect queue-level's behavior, but they share the calendar id and `calendarNameDiscordIdMap` as an internal state.

-   None of the other extensions will see or interfere with this state.
