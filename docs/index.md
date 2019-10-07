# LimitKit

LimitKit provides a simple rate limiting and throttling service for shortcuts. Shortcuts can call LimitKit to learn two things:

1. When was the last time the shortcut ran. 
2. Whether or not a set amount of time has elapsed since the last call to the shortcut. 

> LimitKit works especially well with [Autocuts](#autocuts), a solution for running shortcuts automatically in the background for iOS 13. 

## Example 1: Low Power Mode
Suppose you have a shortcut that activates Low Power Mode when the battery level reaches a certain percentage. You create an Autocut that runs periodically in the background while you use your device during the day. 

you want to run at most once every five minutes. You can use LimitKit to prevent the shortcut from fully running if it was called at the three minute mark. After five or more minutes have passed, LimitKit will tell the shortcut that it's okay to run its main routine. 

Want to have a shortcut that checks your battery every five minutes? It's easy to do with LimitKit, Autocuts, and Personal Automations. 

## Usage

Call the LimitKit shortcut with a dictionary contains the following variables. Items in bold below are required. 

- **shortcut**: the name of your shortcut. 
- **interval**: the number seconds, minutes, hours, weeks, months, or years to wait. 
- **unit**:
		- s (seconds)
		- m (minutes)
		- h (hours)
		- w (weeks)
		- mo (months)
		- y (years)
- update: set this to 1 to update the shortcut's last used timestamp in LimitKit. This is only updated if the shortcut passed LimitKit's test. 

## Examples

Sending the following dictionary to:

```
{
	"shortcut": "Low Power Mode",
	"interval": 5,
	"unit": "m",
	"update": 1
}
```

LimitKit will check first if it has an entry for "Low Power Mode" in its records. If not, it will return 1 to your shortcut and mark the time. 

The next time you call LimitKit with the same information, it will compare the current time with the last time LimitKit was asked to check on the "Low Power Mode" key. 

If at least 5 minutes have passed, LimitKit will return 1. Otherwiss, a 0 is returned. 

With this knowledge, the Low Power Mode shortcut can exit without running the rest of its actions. While the actions skipped are relatively small, you can see how this can be useful if you have a very complicated shortcut. Shortcuts that use location or retrieve large amounts of data from remote servers can benefit by incorporating LimitKit. 