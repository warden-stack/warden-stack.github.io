---
layout: post
title: Cachet integration
---

Thew new **[integration](https://github.com/warden-stack/Warden/wiki/Integration)** with the **[Cachet](https://cachethq.io)** will let you to make use of these beautiful [status pages](https://demo.cachethq.io) along with the running instance of the Warden monitoring application. **[Cachet integration](https://github.com/warden-stack/Warden/wiki/Integration-with-Cachet)** can be configured seamlessly within a few lines of code.

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .IntegrateWithCachet("http://localhost/api/v1", "access_token_or_credentials"), cfg =>
    {
        cfg.WithTimeout(TimeSpan.FromSeconds(3))
           .WithGroupId(1);
    })
    .SetHooks((hooks, integrations) =>
    {
        hooks.OnIterationCompletedAsync(iteration => 
              OnIterationCompletedCachetAsync(iteration, integrations.Cachet()))
    })
    //Configure watchers, hooks etc..

    private static async Task OnIterationCompletedCachetAsync(IWardenIteration iteration,
        CachetIntegration cachet)
    {
        await cachet.SaveIterationAsync(iteration);
    }
```


**[Cachet integration](https://github.com/warden-stack/Warden/wiki/Integration-with-Cachet)** is available as a **[NuGet](https://www.nuget.org/packages/Warden.Integrations.Cachet)** package. 

```
Install-Package Warden.Integrations.Cachet
```
