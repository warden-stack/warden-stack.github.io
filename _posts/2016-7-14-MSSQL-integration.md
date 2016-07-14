---
layout: post
title: MS SQL integration
---

The new type of **[integration](https://github.com/warden-stack/Warden/wiki/Integration)** designed for either querying or saving the data into the MS SQL database has been released. **[MS SQL integration](https://github.com/warden-stack/Warden/wiki/Integration-with-MSSQL)** also provides storing the whole *IWardenIteration* object thanks to the available method *SaveIterationAsync()* and provided table schema, which means you can store all the data you need in your own MS SQL database just within a few lines of code.


Sample usage:

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .IntegrateWithMsSql(@"Data Source=.\sqlexpress;Initial Catalog=MyDatabase;Integrated Security=True")
    .SetHooks((hooks, integrations) =>
    {
	hooks.OnIterationCompletedAsync(iteration => integrations.MsSql()
     	     .QueryAsync<int>("select * from users where id = @id", GetSqlQueryParams()))
     	     .OnIterationCompletedAsync(iteration => integrations.MsSql()
     	     .ExecuteAsync("insert into messages values(@message)", GetSqlCommandParams()));
    })
    //Configure watchers, hooks etc..

private static IDictionary<string, object> GetSqlQueryParams()
    => new Dictionary<string, object> {["id"] = 1};

private static IDictionary<string, object> GetSqlCommandParams()
    => new Dictionary<string, object> {["message"] = "Iteration completed"};
```

Saving the iteration objects:

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .IntegrateWithMsSql(@"Data Source=.\sqlexpress;Initial Catalog=MyDatabase;Integrated Security=True")
    .SetHooks((hooks, integrations) =>
    {
        hooks.OnIterationCompletedAsync(iteration => 
              OnIterationCompletedMsSqlAsync(iteration, integrations.MsSql()));
    })
    //Configure watchers, hooks etc..

private static async Task OnIterationCompletedMsSqlAsync(IWardenIteration wardenIteration,
    MsSqlIntegration integration)
{
    await integration.SaveIterationAsync(wardenIteration);
}
```

Database schema for storing *IWardenIteration*:

```
CREATE TABLE WardenIterations
(
	Id bigint primary key identity not null,
	WardenName nvarchar(MAX) not null,
	Ordinal bigint not null,
	StartedAt datetime not null,
	CompletedAt datetime not null,
	ExecutionTime time not null,
	IsValid bit not null
)

CREATE TABLE WardenCheckResults
(
	Id bigint primary key identity not null,
	WardenIteration_Id bigint not null,
	IsValid bit not null,
	StartedAt datetime not null,
	CompletedAt datetime not null,
	ExecutionTime time not null,
	foreign key (WardenIteration_Id) references WardenIterations(Id)
)

CREATE TABLE WatcherCheckResults
(
	Id bigint primary key identity not null,
	WardenCheckResult_Id bigint not null,
	WatcherName nvarchar(MAX) not null,
	WatcherType nvarchar(MAX) not null,
	Description nvarchar(MAX) not null,
	IsValid bit not null,
	foreign key (WardenCheckResult_Id) references WardenCheckResults(Id)
)

CREATE TABLE Exceptions
(
	Id bigint primary key identity not null,
	WardenCheckResult_Id bigint not null,
	ParentException_Id bigint null,
	Message nvarchar(MAX) null,
	Source nvarchar(MAX) null,
	StackTrace nvarchar(MAX) null,
	foreign key (WardenCheckResult_Id) references WardenCheckResults(Id),
	foreign key (ParentException_Id) references Exceptions(Id)
)
```

**[MS SQL integration](https://github.com/warden-stack/Warden/wiki/Integration-with-MSSQL)** is available as a **[NuGet](https://www.nuget.org/packages/Warden.Integrations.MsSql)** package. 

```
Install-Package Warden.Integrations.MsSql
```