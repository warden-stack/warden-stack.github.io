---
layout: post
title: Warden Spawn first glimpse
---

Warden Spawn is a brand new **[repository](https://github.com/warden-stack/Warden-Spaw)** within the [Warden Stack](https://github.com/warden-stack) that will let you configure the [Warden](https://github.com/warden-stack/Warden/wiki/Warden) instance of the Warden monitoring application using the human readable configuration files.

The ultimate goal is to integrate this tool with the API and later on create a web interface that will let anyone to configure the Warden using some nice editor containing some forms with checkboxes, drop-down lists etc. which under the hood will generate the configuration object and spin up a new instance of the Warden ready to take care of your infrastructure.
Take a look at the following sample configuration file:

```json
{
  "wardenName": "Warden Spawn",
  "integrations": [
    {
      "type": "sendGrid",
      "configuration": {
        "apiKey": "xyz",
        "sender": "spawn@getwarden.net",
        "defaultSubject": "Monitoring",
        "defaultMessage": "Monitoring error(s).",
        "defaultReceivers": [ "warden-spawn@mailinator.com" ]
      }
    },
    {
      "type": "console",
      "configuration": {
        "defaultText": "Hello!"
      }
    }
  ],
  "watchers": [
    {
      "type": "Web",
      "name": "My Web Watcher",
      "configuration": {
        "url": "http://www.google.com",
        "timeout": "00:00:05"
      },
      "hooks": [
        {
          "type": "onCompleted",
          "condition": "invalidCheckResult",
          "use": "sendGrid",
          "configuration": {
            "receivers": [ "warden-spawn2@mailinator.com" ]
          }
        },
        {
          "type": "onCompletedAsync",
          "condition": "validCheckResult",
          "use": "sendGrid",
          "configuration": {
            "subject": "Monitoring async",
            "message": "Monitoring error(s) async."
          }
        },
        {
          "type": "onCompleted",
          "condition": "none",
          "use": "console",
          "configuration": {
          }
        },
        {
          "type": "onCompletedAsync",
          "condition": "none",
          "use": "console",
          "configuration": {
            "text": "Hello async!" 
          }
        }
      ]
    }
  ]
}
```

And here is the actual C# code that creates a Warden instance based on such config file:

```csharp
var configurationReader = WardenSpawnConfigurationReader
    .Create()
    .WithWatcher<WebWatcherSpawn>()
    .WithIntegration<ConsoleSpawnIntegration>()
    .WithIntegration<SendGridSpawnIntegration>()
    .Build();

var configurator = WardenSpawnConfigurator
    .Create()
    .Build();

var factory = WardenSpawnFactory
    .Create()
    .WithConfigurationReader(() => configurationReader)
    .WithConfiguration(File.ReadAllText("configuration.json"))
    .WithConfigurator(() => configurator)
    .Build();

var spawn = factory.Resolve();
var warden = spawn.Spawn();
System.Console.WriteLine($"Warden: '{warden.Name}' has been created and started monitoring.");
Task.WaitAll(warden.StartAsync());
```

This is the first working version of the Warden Spawn and probably a lot of things will change. 
The point is that we want to make the Warden a truly user-friendly tool, not only for the programmers but also for anyone else who might not know the C# but would still like to use this tool in order to make a custom monitoring application. 
