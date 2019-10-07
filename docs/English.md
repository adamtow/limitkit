# LimitKit

LimitKit brings rate limiting and throttling to Shortcuts. It allows shortcuts that are run automatically via Personal Automations in iOS 13 to control how frequently they can run their primary function. 

Suppose you have a shortcut that you want to run once every five minutes. You can use LimitKit to prevent the shortcut from fully running if it was called at the three minute mark. LimitKit will tell you when five minutes or more minutes have passed, at which point the shortcut can run its main routine. 

LimitKit is especially useful when paired with [Autocuts](https://tow.com/Shortcuts/autocuts), the shortcut for automatically running your shortcuts while you use your iOS device. 

Want to have a shortcut that checks your battery every five minutes? It's easy to do with LimitKit, Autocuts, and Personal Automations. 