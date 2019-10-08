# LimitKit

LimitKit provides a simple rate limiting and throttling service for shortcuts. Shortcuts can call LimitKit to learn two things:

1. When was the last time the shortcut ran. 
2. Whether or not a set amount of time has elapsed since the last call to the shortcut. 

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

## Example 1: Low Power Mode
Suppose you have a shortcut that activates Low Power Mode when the battery level reaches a certain percentage. You create an Autocut that runs periodically in the background while you use your device during the day. 

you want to run at most once every five minutes. You can use LimitKit to prevent the shortcut from fully running if it was called at the three minute mark. After five or more minutes have passed, LimitKit will tell the shortcut that it's okay to run its main routine. 

Want to have a shortcut that checks your battery every five minutes? It's easy to do with LimitKit, Autocuts, and Personal Automations. 

## Example 1: QA: LimitKit Test

Consider the [QA: LimitKit Test](https://www.icloud.com/shortcuts/f1b53984116345b78911d8b93be34132) shortcut. It is set to run its primary function every minute. It sends the following dictionary to LimitKit which returns 0 of it failed the time limit check or 1 if it passed:

```
{
	"key": "LimitKit Test",
	"interval": 60,
	"unit": "s",
	"update": 1,
	"delete": 0
}
```

![Test Shortcut with LimitKit support](https://adamtow.github.io/limitkit/images/limitkit-test.png)

LimitKit will check first if it has an entry for "LimitKit" in its records. If not, it will return 1 to your shortcut and mark the time. 

Now run the shortcut again before a minuteras passed. It will exit because it failed LimitKit's time check. If you wait for the minute to pass and run the shortcut again, it will return 1. 

## Example 2: Low Power Mode

The [Low Power Mode shortcut](https://www.icloud.com/shortcuts/86a571aab5334fa590be833d8f03f55b) is used with Autocuts to automatically turn on Low Power Mode when your battery reaches a certain percentage. By default it performs the check once every five minutes thanks to its integration with LimitKit. 

## Example 3: Location Triggers