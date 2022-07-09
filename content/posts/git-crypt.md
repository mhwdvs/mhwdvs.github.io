---
title: Using git-crypt
author: "Matthew Davis"
date: "2022-01-05"
description: "git-crypt is a simple tool for encrypting files in a git repository"
---

`git-crypt` is a simple tool for encrypting files in a git repository. It's good for both text and binary files, and ideal for public repositories, protecting files that are both sensible to source control (ie. that shouldn't be in your `.gitignore`), but contain sensitive info you might not want public. My first use case is for my Nix/NixOS/home-manager configuration ([https://github.com/mhwdvs/hm/]()), where I need to add some sensitive configuration details for work, but still want to source control my changes and conveniently access the configuration across multiple machines.

Let's get started.

## I haven't used git-crypt before and don't have a key yet, or I want a new key for every repository I use `git-crypt` in

- `git-crypt init`
- `git-crypt export-key ./git-crypt-key` (will output file `git-crypt-key`, your symmetric key)

## I already have a key from git-crypt and I want to use it for another repository

- Commit at least one file to the repository (not one that should be encrypted). A good first file may be a `README.md` or `.gitignore`
- `git-crypt init`
- `git-crypt decrypt <common PGP key file>`

## Add your key file to your repositories' `.gitignore`

It's vital that you **don't** commit your key file to your repository. Append the name of your key file to your repositories `.gitignore` so that git ignores it.

## Create/Append `.gitattributes`

This is where we add rules that tell git-crypt what files it should encrypt. `.gitattributes` should be created at the root of your git repository if it doesn't already exist. The lines you add to it for `git-crypt` should look something like this;

```
<file name> filter=git-crypt diff=git-crypt
```

The file name can be a literal file name, or utilise wildcards (`*`) to target many files (eg. `*.key` to encrypt all files ending in `.key`), or use any other logic git supports in it's `.gitattributes`.

Commit this file to your git repository.

## Add your secret files

What the heading says.

`git-crypt status` will tell you what files it is managing. Note that you don't have to commit secret files to your repository for them to show as "encrypted" in this dialog, so you have the opportunity to check that everything is working as intended before committing your secrets and potentially having them be pushed out into the public.

