---
layout: post
title: Server watcher
---

Previously existing **Port watcher** has been extended with a new feature for sending a ping requests (including status description in response), therefore it has been refactored into the new **[Server watcher](https://github.com/warden-stack/Warden/wiki/Watcher-type-Server)**.
Port is now an optional parameter, as from now on this **watcher** serves a more generic purpose.

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .AddServerWatcher("www.google.com", 80)
    //Configure other watchers, hooks etc.
```

**[Server watcher](https://github.com/warden-stack/Warden/wiki/Watcher-type-Server)** is available as a *NuGet* package. 
Additionally, please download the latest version of the **[HTTP API Integration](https://github.com/warden-stack/Warden/wiki/Integration-with-HTTP-API)**, as some serialization issue had to be resolved in order to work with a new watcher.
