---
title: Node.js
date: 2025-11-22
category: integrations
author: Kaeso
description: How to use Skyblock Repo in Node.js projects
tags:
  - integration
  - tutorial
  - Node.js
  - JavaScript
published: true
---

`skyblockrepo` is a TypeScript/JavaScript client for interacting with the Skyblock Repo data. It allows you to easily load item data, search for items, and match items from your application to the repo data.

## Installation

You can install the package via your favorite package manager:

```bash
npm install skyblockrepo
# or
pnpm add skyblockrepo
# or
yarn add skyblockrepo
```

## Usage

### Initialization

First, you need to initialize the client. This will download the latest data from the repository (and cache it locally).

```typescript
import { SkyblockRepoClient } from 'skyblockrepo';

const client = new SkyblockRepoClient();

// Initialize the client (downloads data if needed)
await client.initialize();
```

### Updates

The client checks for updates once during initialization. It does **not** automatically check for updates on an interval. If you have a long-running application and want to keep the data fresh, you should implement your own interval to call `checkForUpdates`.

```typescript
// Check for updates every hour
setInterval(async () => {
  await client.checkForUpdates();
}, 1000 * 60 * 60);
```

### Finding Items

You can find items by their internal ID or by name.

```typescript
// Find by ID
const hyperion = client.findItem('HYPERION');
console.log(hyperion?.name); // "Hyperion"

// Find by Name (fuzzy search)
const aspectOfTheEnd = client.findItem('Aspect of the End');
console.log(aspectOfTheEnd?.internalId); // "ASPECT_OF_THE_END"
```

### Accessing Data

You can access the raw data directly through the `data` property.

```typescript
const allItems = client.data.items;
const pets = client.data.pets;
const npcs = client.data.npcs;
```

### Configuration

You can customize the configuration when creating the client.

```typescript
const client = new SkyblockRepoClient({
  fileStoragePath: './my-cache', // Custom cache directory
  useNeuRepo: true, // Enable loading NEU repo data (e.g. for skins)
  skyblockRepo: {
    // Custom repo settings
    name: 'skyblockrepo',
    url: 'https://github.com/SkyblockRepo/Repo',
    zipPath: '/archive/refs/heads/main.zip',
    apiEndpoint: 'https://api.github.com/repos/SkyblockRepo/Repo/commits/main',
  },
  neuRepo: {
    // Custom NEU repo settings
    name: 'neu',
    url: 'https://github.com/NotEnoughUpdates/NotEnoughUpdates-REPO',
    zipPath: '/archive/refs/heads/master.zip',
    apiEndpoint: 'https://api.github.com/repos/NotEnoughUpdates/NotEnoughUpdates-REPO/commits/master',
  },
  // Custom logger (optional)
  logger: {
    log: (msg, ...args) => console.log('[RepoJS]', msg, ...args),
    warn: (msg, ...args) => console.warn('[RepoJS]', msg, ...args),
    error: (msg, ...args) => console.error('[RepoJS]', msg, ...args),
  }
});
```

### Item Matching

You can register matchers to automatically match your own item objects to Skyblock Repo items.

```typescript
import { SkyblockRepoMatcher } from 'skyblockrepo';

class MyItem {
  constructor(public id: string, public displayName: string) {}
}

const myItemMatcher: SkyblockRepoMatcher<MyItem> = {
  getSkyblockId: (item) => item.id,
  getName: (item) => item.displayName,
};

client.registerMatcher(MyItem, myItemMatcher);

const myItem = new MyItem('HYPERION', 'Hyperion');
const match = client.matchItem(myItem);

if (match) {
  console.log('Matched item:', match.item.name);
}
```
