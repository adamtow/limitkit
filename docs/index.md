# LimitKit

LimitKit provides a simple rate limiting and throttling service for shortcuts. Shortcut developers can use LimitKit to:

1. Track when the last time the shortcut ran a certain operation.
2. Calculate whether a set amount of time has elapsed since the last successful call to LimitKit. 

LimitKit works especially well with shortcuts used with [Autocuts](#autocuts), a solution for running shortcuts automatically in the background for iOS 13. 

## Usage

Call the LimitKit shortcut with either a string or a dictionary contains the following variables. Items in bold below are required. 

### Dictionary

- **key**: typically the  name of your shortcut, but it can be any string that uniquely identifies the operation you want to rate limit. 
- **interval**: the number seconds, minutes, hours, weeks, months, or years to wait. 
- **unit**:
		- s (seconds)
		- m (minutes)
		- h (hours)
		- w (weeks)
		- mo (months)
		- y (years)
- update: set this to 1 to update the last used timestamp in LimitKit for the given key. This is only updated if the shortcut key passed LimitKit's test.
- delete: set this to 1 to delete the key's record from LimitKit. 

### String

Sending a string to LimitKit will ignore any time check and set the current time as the new last run time for the key. LimitKit will return a dictionary containing:

- lastRun: when LimitKit was last run with the given key.
- count: the number of times LimitKit has been called for the given key.

****

## Examples
This section shows three shortcuts that utilize LimitKit:

### Example 2: QA: LimitKit Test

Consider the [LimitKit Test](https://www.icloud.com/shortcuts/6555a25b14d94f28a7aea06a9be91eb0) shortcut. It sends the following dictionary to LimitKit which returns 0 if the call was made in the last 60 seconds of a successful call. After 60 seconds have elapsed, LimitKit will return 1 and start the countdown again.

```
{
	"key": "LimitKit Test",
	"interval": 60,
	"unit": "s",
	"update": 1,
}
```

![Test Shortcut with LimitKit support](https://adamtow.github.io/limitkit/images/limitkit-test.png)

LimitKit will check first if it has an entry for "LimitKit Test" in its records. If not, it will create a new record, mark the current time, and return 1 to your shortcut. 

Now run the shortcut again before 60 seconds have passed. LimitKit will now return a 0 since it failed the time interval check. If you run the shortcut again  after 60 seconds, it will work as expected.

### Example 2: Auto Low Power Mode

The [Auto Low Power Mode shortcut](https://www.icloud.com/shortcuts/3af0ea02f13a4f6bbb851b2fe97d644a) is used with Autocuts to automatically turn on Low Power Mode when your battery reaches a certain percentage. By default it performs the check once every five minutes thanks to its integration with LimitKit.

{
	"key": "Auto Low Power Mode",
	"interval": 5,
	"unit": "m",
	"update: 1
}

In Autocuts Admin, I created a new Autocut with the following properties:

- shortcut: Auto Low Power Mode
- expires: -1
- device: the name of my device
- activate: 0

Autocuts is configured to run every time I open my most frequently used apps, such as Mail, Safari, Notes, and Messages, but it only runs Autocuts every one minute due to its own integration with LimitKit. Every 5 minutes, the Auto Low Power Mode shortcut will run and evaluate my current battery level. 

### Example 3: Location Triggers

A more advance use of LimitKit comes in the form of the Location Triggers shortcut. In conjunction with Autocuts, Location Triggers runs shortcuts automatically in the background based on your current location while you use your iOS device.

Because retrieving the user's current location can be a time-consuming and battery hogging operation, LimitKit is used to restrict location checks every 5-10 minutes. If I need

![Location Triggers with LimitKit support](https://adamtow.github.io/limitkit/images/location-triggers-limitkit.png)

Location Triggers has a UI for activating LimitKit integration and for setting the interval value. 

## Application Interface

When LimitKit is launched with no parameters, it displays a 