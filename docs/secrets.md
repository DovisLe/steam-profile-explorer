# Keeping the Steam API key safe

The Steam Web API key works like a password for API calls. This repo is public,
so the key must never be committed. Git keeps every commit forever, so deleting
a file later does not remove a key from history.

## Where the key lives

Only in `dotnet user-secrets`, which saves it outside this folder:

```
%APPDATA%\Microsoft\UserSecrets\<UserSecretsId>\secrets.json
```

Set or change it from the project folder:

```
dotnet user-secrets set "Steam:ApiKey" "<your key>"
dotnet user-secrets list
```

The `UserSecretsId` in the `.csproj` file is not secret. It only links the
project to that file.

## The protections in this repo

| Layer | What it does |
| --- | --- |
| `.gitignore` | Ignores `secrets.json`, `.env` files and other local config, so `git add .` skips them. |
| `.githooks/pre-commit` | Runs before every commit. Blocks secret files and any added line with 32 hex characters in a row (the shape of a Steam key). |
| `.github/workflows/secret-scan.yml` | Runs on GitHub for every push and pull request. Fails if a key-like value is in the files or the history, so you know to revoke it. |

Turn the hook on once after cloning (git does not copy this setting):

```
git config core.hooksPath .githooks
```

## Rules

1. Never paste the key into code, `appsettings.json`, a `.http` file, a test, a
   README, a chat, a screenshot or an issue.
2. Never commit with `--no-verify`. That skips the hook.
3. Before every commit, read `git status` and `git diff --staged`.
4. Only register keys at https://steamcommunity.com/dev/apikey, typed by you,
   never through a link someone sent.

## If the key leaks

1. Go to https://steamcommunity.com/dev/apikey and click **Revoke My Steam Web
   API Key**. The old key stops working at once.
2. Register a new key and save it with `dotnet user-secrets set`.
3. Revoking is the fix. Removing the commit is not enough, because anyone may
   already have copied it.
4. If you see a key on that page you did not create, revoke it and change your
   Steam password.
