---
layout: post
title: Intervals feature
---

**[Interval](https://github.com/warden-stack/Warden/wiki/Interval)**  is a brand new functionality, that I've been asked to implement at least for a few times. 
By using **intervals**, you are able to specify a custom period for each particular **[Watcher](https://github.com/warden-stack/Warden/wiki/Watcher)** (or all of them at once) after which the actual check (invoking *ExecuteAsync()* method) will happen.

**Let's say you want to ping a website every 500 ms, but on the other hand, a database should be checked only every 10 seconds - and that's where the intervals come in handy**.

Using different **intervals** also affects the general idea behind the iteration, yet in the end I've managed to solve this, so everything works nicely (thanks to the brilliant idea by one of the members of [KGD.NET](http://www.meetup.com/KGD-NET) during my presentation a week ago).

**Intervals are very easy to configure** - it's just an additional (optional) parameter named *interval* being of type *TimeSpan*. You can pass your custom parameter either while using the generic *AddWatcher()* method or any of the available extension methods like *AddWebWatcher()*. To find out more about intervals, please refere to this **[article](https://github.com/warden-stack/Warden/wiki/Interval)**.

```csharp
var configuration = WardenConfiguration
    .Create()
    .AddWebWatcher("http://httpstat.us/200", 
       interval: TimeSpan.FromMilliseconds(500))
    //Configure other watchers, hooks etc.
```

In order to use **intervals** please install version 1.1.0 (or higher) of the *NuGet* packages.
