# 🪝🪝🪝 Git Multi-Hook

- Multiple global git hooks
- Multiple per-repo git hooks
- Existing hooks keep working
- All hooks run in parallel
- No configuration files

Configure Git hooks once, use them everywhere. No more copy-pasting hooks between repos like it's 2005.


## 🎯 How It Works

When you trigger any Git hook (like `pre-commit`), Git Multi-Hook runs all of the following triggers at the same time, in parallel.

1. **Global hooks** → all scripts in `./hooks/pre-commit.d/*`
2. **Repo hooks** → all scripts in `$YOUR_REPO/.git/hooks/pre-commit.d/*`
3. **Default hook** → `$YOUR_REPO/.git/hooks/pre-commit`

Within each category, scripts are started in alphabetical order (use `01-`, `02-` prefixes for ordering).

All the hooks run at the same time (up to the number of CPUs in the computer), and then results are reported in launch order, one after another until a hook reports failure or all succeed.

Existing hooks work with no changes, except care must be taken to install `git-lfs` to avoid overwriting existing hooks; see [Caveats](#Caveats) below.


## 🚀 Installation

Run the setup script and you're good to go:

```bash
./setup
# => creates hooks and sets gitconfig core.hooksPath
```


## Caveats

Installing Git LFS requires a little bit of extra magic to make sure the LFS hooks are installed in the correct directory, sorry!

```bash
git -c core.hooksPath="$(git rev-parse --git-dir)/hooks" lfs install

# Or use the alias created by the setup script
git lfs-install
```


## ✨ Adding Your Own Hooks

Want to add a new global hook? Easy peasy:

```bash
# Create the directory for the type of hook you want (if it doesn't exist already)
mkdir -p hooks/pre-commit.d

# Drop your script in there
cp your-awesome-script.sh hooks/pre-commit.d/

# Make it executable
chmod +x hooks/pre-commit.d/your-awesome-script.sh
```

Add repo-specific git hooks too (e.g. copy to `$YOUR-REPO/.git/hooks/pre-commit.d/`)


## 📚 Supported Hooks

ALL standard Git hooks supported:

### 💻 Client-Side Hooks
- **`pre-commit`** → Last chance to check before committing
- **`prepare-commit-msg`** → Auto-generate or modify commit messages
- **`commit-msg`** → Validate commit message format (hello, conventional commits!)
- **`post-commit`** → Celebrate after a successful commit 🎉
- **`pre-rebase`** → Safety checks before rewriting history
- **`post-checkout`** → Run after switching branches
- **`post-merge`** → Clean up or notify after merges
- **`pre-push`** → Final checks before code leaves your machine
- **`post-rewrite`** → Triggered by `git commit --amend` and rebases
- **`pre-merge-commit`** → Validate before creating a merge commit

### 📧 Email Patch Hooks
- **`applypatch-msg`** → Edit commit messages from patches
- **`pre-applypatch`** → Validate patches before applying
- **`post-applypatch`** → Run after successfully applying patches
- **`sendemail-validate`** → Check patches before emailing

### 🖥️ Server-Side Hooks
- **`pre-receive`** → Gatekeeper for incoming pushes
- **`update`** → Validate each ref being updated
- **`post-receive`** → Trigger CI/CD or notifications
- **`post-update`** → Run after all refs are updated
- **`push-to-checkout`** → Handle non-bare repository pushes


## 🔍 Debugging

Something not working? Turn on verbose mode to see what's happening:

```bash
VERBOSE=2 git commit -m "debugging time!"
```

Shows you which hooks are running in what order.


## 🚫 Disabling Hooks

### 🤔 Skip hooks for one command ("I know what I'm doing" mode)
```bash
git --no-verify commit -m "living dangerously"
```

### ☢️ Turn off globally (nuclear option)
```bash
git config --global --unset core.hooksPath
```


## 🎁 Bonus: Batteries (Hooks) Included!

This repository includes several hooks already pre-configured.

### Pre-Commit Hooks

- `end-with-blank-line`: Ensures staged source and config files end with a trailing newline.
- `find-do-not-commit`: Blocks commits ehen staged files contain `DO NOT COMMIT` (set `IGNORE_DO_NOT_COMMIT=1` to warn instead).
- `lint-code`: Runs `$REPO/scripts/lint` on staged files if it exists.
- `lint-nodejs`: Runs `npm|yarn|pnpm run lint` if `$REPO/package.json` has a `lint` script.
- `lint-shellcheck`: Runs `shellcheck` on staged shell scripts (set `IGNORE_SHELLCHECK=1` to skip).
- `lint-swift`: Runs `swiftlint` on staged `.swift` files when `.swiftlint.yml` is present (set `IGNORE_SWIFT_LINT=1` to skip).
- `prevent-commit-secrets`: Runs `ripsecrets` against staged files (set `IGNORE_SECRETS=1` to skip).
- `validate`: Runs `$REPO/scripts/validate` on staged files if it exists.


## 🤝 Contributing

Found a bug? Have an awesome hook to share? PRs are welcome!


# 🪪 License

Apache License, Version 2.0

git-multi-hook Copyright © 2026 Patrick Wyatt

See [LICENSE.md](LICENSE.md) for details.


# 😻 Thanks to

Thanks to [Simon Epskamp](https://stackoverflow.com/users/146738/simon-epskamp). This repo is based on his [stackoverflow post](https://stackoverflow.com/a/61341619).
