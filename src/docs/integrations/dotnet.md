---
title: C#
date: 2025-08-21
category: integrations
author: Kaeso
description: How to use Skyblock Repo in .NET projects
tags:
  - integration
  - tutorial
  - .NET
  - C#
published: true
---

A [NuGet package](https://www.nuget.org/packages/SkyblockRepo) is available to use to easily interact with the raw data from the [Skyblock Repo].

If you need extra help, make an issue on the [GitHub repository].

## Installation

You can add the library to your project with:

```sh
dotnet add package SkyblockRepo
```

## Setup

Right now, the library is in a basic prelease state.

It can be registered into a dependency injection environment as shown below, all services are Singleton services.

Here's how it's set up internally:

```csharp
public static IServiceCollection AddSkyblockRepo(this IServiceCollection services, SkyblockRepoConfiguration options) {
  services.AddSingleton(options);
  services.AddSingleton<ISkyblockRepoUpdater, SkyblockRepoUpdater>();
  services.AddSingleton<ISkyblockRepoClient, SkyblockRepoClient>();
  return services;
}
```

### Add the repo from remote source

By default, the repo is automatically downloaded from https://github.com/SkyblockRepo/Repo on initialization.
If an update is found, it'll automatically be downloaded and extracted.

```csharp
using SkyblockRepo;

builder.Services.AddSkyblockRepo(config => {
  // Path for where you want the repo to be extracted to, defaults to an OS-specific local app data folder
  config.FileStoragePath = /**/;

  // You can change this to a fork if needed, defaults to the main repo
  // Also change config.SkyblockRepoApiEndpoint if you change this
  config.SkyblockRepoUrl = "https://github.com/SkyblockRepo/Repo";
});
```

**You will need to implement update polling yourself!**

This is as simple as calling `ISkyblockRepo.CheckForUpdatesAsync()` every 10 minutes / 30 minutes / etc.

### Example polling implementation

Background service example for polling updates. You can implement this however you want though.

```csharp
public class RepoUpdateService(ISkyblockRepoClient repoClient) : BackgroundService
{
	private readonly TimeSpan _interval = TimeSpan.FromMinutes(10);

	protected override async Task ExecuteAsync(CancellationToken stoppingToken)
	{
		while (!stoppingToken.IsCancellationRequested)
		{
			await Task.Delay(_interval, stoppingToken);
			await repoClient.CheckForUpdatesAsync(stoppingToken);
		}
	}
}
```

```csharp
// Program.cs
builder.Services.AddHostedService<RepoUpdateService>();
```

### Add a local copy of the repo

You can use a local copy for running tests or managing the updates/version outside of your project.

Specifying a local path disables all automatic polling/updates.

```csharp
using SkyblockRepo;

builder.Services.AddSkyblockRepo(config => {
  // An example with the repo being cloned in a folder next to your project
  config.LocalRepoPath = Path.Combine(SkyblockRepoUtils.GetSolutionPath, "..", "SkyblockRepo");
});
```

## Usage

After setting up the repo, you'll need to initialize it.

```csharp
// Example init in Program.cs
using (var scope = app.Services.CreateScope())
{
  var repo = scope.ServiceProvider.GetRequiredService<ISkyblockRepoManager>();
  await repo.InitializeAsync();
}
// Or statically (though Instance won't be initialized until the service host is built)
await SkyblockRepoClient.Instance.InitializeAsync();
```

Then you can inject `ISkyblockRepoClient` and use the methods on it!

```csharp
public class MyService(ISkyblockRepoClient repo)
{
  public SkyblockItemData? FindItem(string name) => repo.FindItem(name);
}
```

You can also access the repo data in a static context like this:

```csharp
var item = SkyblockRepoClient.Instance.FindItem(itemName);
```

## Setup outside of DI

Using the SkyblockRepo outside of dependency injection isn't really supported at this time.

You _can_ get it working like this though:

```csharp
// You need an ILogger instance, this is a substitue using NSubstitue
var logger = Substitute.For<ILogger<SkyblockRepoUpdater>>();

var config = new SkyblockRepoConfiguration
{
  /* Your config options */
};
var updater = new SkyblockRepoUpdater(config, logger);
var repo = new SkyblockRepoClient(updater);

await repo.InitializeAsync();

// You'd then reference it elsewhere with SkyblockRepoClient.Instance
```

These docs will be expanded as more features are added!

[Skyblock Repo]: https://github.com/SkyblockRepo/Repo
[Github repository]: https://github.com/SkyblockRepo/RepoUpdater
