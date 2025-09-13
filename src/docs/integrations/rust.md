---
title: Rust
date: 2025-09-11
category: integrations
author: not a cow
description: How to use Skyblock Repo in Rust projects
tags:
  - integration
  - tutorial
  - rust
published: true
---

A [Cargo package](https://crates.io/crates/skyblock-repo) is available to use to easily interact with the raw data from the [Skyblock Repo]

If you need extra help, make an issue on the [GitHub repository].

## Installation

You can add the library to your project with:

```sh
cargo add skyblock-repo
```

## Usage

The library provides **very simple** methods to retrieve the data for specific items.

### Dealing with the raw repo

`download_repo` downloads the repo and extracts its content

Attempting to download the repo while the `SkyblockRepo` directory already exists will return `Ok(())` prematurely. Additionally, if the `log` feature is enabled, print a warning to the stdout.

`delete_zip` param determines whether or not to delete the zip download and only keep the extracted files.

```rust
download_repo(true)?;
```

`delete_repo` deletes the `SkyblockRepo` directory and `SkyblockRepo-main.zip` if they exist.

```rust
delete_repo()?;
```

### Using the library to interop with the repo

First, you need to initialize the SkyblockRepo data.

```rust
let repo = SkyblockRepo::new()?;
```

Then you can fetch data for a specific item.

On success, these return `Some(<data>)`, else `None`.

```rust
let enchantment: SkyblockEnchant = repo.get_enchantment_by_id("TELEKINESIS");
let item: SkyblockItem = repo.get_item_by_id("STAR_SWORD_9000");
let pet: SkyblockPet = repo.get_pet_by_id("PHOENIX");
```

[Skyblock Repo]: https://github.com/SkyblockRepo/Repo
[Github repository]: https://github.com/SkyblockRepo/RepoRS
