# LimitKit

LimitKit is an event logging system and rate limiting tool for shortcuts. Shortcut developers can use LimitKit to:

1. Track and log events made in their shortcuts.
2. Rate limit shortcut operations by calculating whether a set amount of time has elapsed since the last successful log event.

![LimitKit](https://adamtow.github.io/limitkit/images/limitkit.png)

Check out the example shortcuts below that all employ LimitKit:

- **[LimitKit Test](#limitkit-test)**: A simple shortcut demonstrating LimitKit's rate limiting abilities.
- **[Auto Low Power](#auto-low-power)**: A shortcut that works with Autocuts to activate Low Power Mode automatically when your battery falls to a certain percentage.
- **[Auto DND](#auto-dnd)**: A shortcut that works with Autocuts to activate Do Not Disturb mode whenever a calendar event has the string "DND" in its title or notes field.
- **[Location Triggers](#location-triggers)**: A shortcut that runs shortcuts automatically based on your current location.

## Download
The latest version of LimitKit can be found on RoutineHub:

- [**Download LimitKit on RoutineHub**](https://routinehub.co/shortcut/3603)

## Autocuts

LimitKit works especially well with [Autocuts](https://adamtow.github.io/autocuts) — the solution for running shortcuts automatically in the background while you use your iOS 13 device — because it allows background shortcuts to limit the frequency by which they call their primary function by seconds, minutes, hours, days, weeks, months, or even years.

## API Documentation

Developers call the LimitKit shortcut with either a string or a dictionary containing the following parameters. Items in bold below are required. 

### String

Sending a string to LimitKit will perform a `track` command request for the given event name. LimitKit will return a dictionary containing:

- event: the name of the event to be tracked.
- date: the last time the track request was made.
- count: the number of times LimitKit has been called for the given event name.

### Dictionary

- `event`: the name of the event to be tracked.
- command: what type of request to make to LimitKit. Possible values include:
	- `value`: return the entry object but perform no track or verify request.
	- `track`: update the entry and return an object containing the last run date and the number of times the entry has been tracked.
	- `test`: given an **interval** and **unit**, return whether enough time has passed from the last run date.
	- `update`: performs a `test` and updates the event record if the test was successful.
	- `delete`: delete the entry by creating a new entry with the last run date of January 1, 1970 12:00 AM UTC ([UNIX Epoch Time](https://en.wikipedia.org/wiki/Unix_time)).
- `interval`: the number seconds, minutes, hours, weeks, months, or years to wait. 
- `unit`: the time frame by which to compare the last run date with the current date and time:
		- `s` (seconds)
		- `m` (minutes)
		- `h` (hours)
		- `w` (weeks)
		- `mo` (months)
		- `y` (years)
	- `properties`: Text string of information to store with the event during `track` and `update` calls.

**NOTE**: *If no command is specified in a dictionary, an `update` call will be made. Be sure to include valid values for interval.*

### Event Names

The event name string is case-insensitive. Whitespace will be removed from the beginning and end of the line, and the event name must be on a single line. If you are tracking operations for your shortcut, you can consider using unique strings prefixed by the name of your shortcut. For instance:

- Autocuts-launch: When the Autocuts shortcut was last launched
- Autocuts-update: When the Autocuts shortcut was last updated
- Autocuts-command: When the Autocuts shortcut last ran a particular command

### Properties

You can store a string of text with your `track` and `update` calls. LimitKit **does not** store anything but the previous event in its database. So, if you want to access records of previous `track` and `update` calls, you will have to manage them yourself.

****

## Examples
This section shows four shortcuts that utilize LimitKit:

<span id="#limitkit-test"></span>
### Example 2: LimitKit Test

Consider the [LimitKit Test shortcut](https://www.icloud.com/shortcuts/af311493352f458c9e7caa9c53b7d92a). It sends the following request dictionary to LimitKit which returns 0 if the call is made before 60 seconds have elapsed following a successful `update` request to LimitKit. Keep running the shortcut again, and you will see 0 until 60 seconds have elapsed.

LimitKit will then return 1 and start the countdown again.

```
{
	"event": "LimitKit Test",
	"interval": 60,
	"unit": "s"
}
```

![Test Shortcut with LimitKit support](https://adamtow.github.io/limitkit/images/limitkit-test.png)

<span id="#auto-low-power-mode"></span>
### Example 2: Auto Low Power Mode

The [Auto Low Power Mode shortcut]() is used with Autocuts to automatically turn on Low Power Mode when your battery reaches a certain percentage. By default it performs the check once every five minutes thanks to its integration with LimitKit.

{
	"event": "Auto Low Power Mode",
	"interval": 5,
	"unit": "m",
}

In Autocuts Admin, create a new Autocut with the following properties:

- shortcut: Auto Low Power Mode
- expires: -1
- device: the name of my device
- activate: 0

Autocuts is configured to run every time the user opens his or her most frequently used apps, such as Mail, Safari, Notes, and Messages. Every 5 minutes, the Auto Low Power Mode shortcut will run and evaluate my current battery level.

> Autocuts itself can be configured to use LimitKit. To prevent unnecessary calls when switching quickly between apps, consider setting an interval of one minute or greater within Autocuts Admin for Autocuts.

![Setting a check frequency for Autocuts](https://adamtow.github.io/limitkit/images/autocuts-limitkit.png)

<span id="#auto-dnd"></span>
### Example 3: Auto DND

The [Auto DND]() shortcut works with Autocuts to automatically toggle Do Not Disturb mode when the current time coincides with calendar events whose title or notes field has the string "DND".

The shortcut uses Get Calendar Events, which can take a variable amount of time depending on how many events are on the device's calendar. LimitKit can be used to check the calendars every 5 minutes instead of every time Autocuts runs.

![Auto DND setting Do Not Disturb automatically](https://adamtow.github.io/auto-dnd/images/auto-dnd-in-action.png)

<span id="#location-triggers"></span>
### Example 4: Location Triggers

A more advanced use of LimitKit comes in the form of the [Location Triggers shortcut](https://adamtow.github.io/location-triggers). In conjunction with Autocuts, Location Triggers runs shortcuts automatically in the background based on your current location while you use your iOS device.

Because retrieving the user's current location can be a time-consuming and battery hogging operation, LimitKit is used to restrict location checks every 5-10 minutes.

This interval value is fully configurable by the user by going to the Settings page in Location Triggers:

![Test Shortcut with LimitKit support](https://adamtow.github.io/limitkit/images/location-triggers-limitkit.png)

****

<span id="#ui"></span>
## Application Interface

When LimitKit is launched with no parameters, it displays a menu with the following commands:

- **Request Assistant**: A step-by-step wizard for creating a request to LimitKit. 
- **New Raw Request**: Expert users can send a raw request by modifying a dictionary object and sending it to LimitKit.
- **View Events**: Displays all the events recorded by LimitKit.
- **Delete All Events**: Clears the LimitKit database of all records.
- **About LimitKit**: Displays about information, include the current version and build number.
- **Help**: Displays the page you are reading now.
- **Check for Updates**: Checks RoutineHub.co if there is a [newer version of LimitKit](https://routinehub.co/shortcut/3603) available.

Tapping **View Events** along with a event name will show you the same information if you called LimitKit with the `value` command.

![Viewing a LimitKit Entry](https://adamtow.github.io/limitkit/images/limitkit-view-entries.png)

**NOTE:** *Editing the values and tapping Done does not save the changes back to the LimitKit entry.*

## Sending Requests to LimitKit
There are two ways you can send a request to LimitKit from the LimitKit shortcut itself.

### Request Assistant
Tap on Request Assistant from the LimitKit Home screen to experience a step-by-step guide for creating a LimitKit request. Here's an example of using the Request Assistant to make a track call.

1. Tap **Request Assistant**.
2. Enter `My Event Name`.
3. Tap **Track**.

The call will be made and the event object will be returned to the user. The date in the event object will be the date when the event was processed by LimitKit.

![Sending a track request using the Request Assistant](https://adamtow.github.io/limitkit/images/limitkit-request-assistant-track.png)

To make an update call, perform the following steps:

1. Tap **Request Assistant**.
2. Enter `My Event Name`
3. Tap **Test**.
4. Enter the interval value.
5. Enter the unit.
6. Tap **Update** if you want the entry updated after a successful evaluation. Tap **Test** if you just want to know if the interval and unit are valid.
7. The response of 0 or 1 is return by LimitKit.

![Creating an update request to LimitKit](https://adamtow.github.io/limitkit/images/limitkit-request-assistant-update.png)

![Sending a successful update request to LimitKit](https://adamtow.github.io/limitkit/images/limitkit-request-assistant-update-success.png)

### Raw Request

![Sending a raw request to LimitKit](https://adamtow.github.io/limitkit/images/limitkit-raw-request.png)