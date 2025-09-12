---
title: Python
date: 2025-09-11
category: integrations
author: not a cow
description: How to use Skyblock Repo in Python projects
tags:
  - integration
  - tutorial
  - python
published: true
---

A [PyPi package](https://pypi.org/manage/project/skyblock-repo) is available to use to easily interact with the raw data from the [Skyblock Repo]

## Installation

You can add the library to your project with:

```sh
pip install skyblock-repo
```

## Usage

The library provides **very simple** methods to retrieve the data for specific items.

### Dealing with the raw repo

`download_repo` downloads the repo and extracts its content.

Attempting to download the repo while the `SkyblockRepo` directory already exists will return prematurely.

`delete_zip` param determines whether or not to delete the zip download and only keep the extracted files, it is `True` by default.

```python
try:
  download_repo()
except e:
  print(f'Failed to download repo: {e}')
```

`delete_repo` deletes the `SkyblockRepo` directory and `SkyblockRepo-main.zip` if they exist.

```python
try:
  delete_repo()
except e:
  print(f'Failed to delete repo: {e}')
```

### Using the library to interop with the repo

First, you need to initialize the SkyblockRepo data.

```python
try:
  repo = SkyblockRepo()
except e:
  print(f'Failed to initialize repo: {e}')
```

Then you can fetch data for a specific item.

On success, these return the data, else `None`.

```python
enchantment = repo.get_enchantment_by_id("TELEKINESIS");
item = repo.get_item_by_id("STAR_SWORD_9000");
pet = repo.get_pet_by_id("PHOENIX");
```

[Skyblock Repo]: https://github.com/SkyblockRepo/Repo
