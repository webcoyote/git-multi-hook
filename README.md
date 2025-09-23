# 🪝🪝🪝 Git Multi-Hook

- Multiple global git hooks
- Multiple per-repo git hooks
- Existing hooks keep working
- No configuration files

Configure Git hooks once, use them everywhere. No more copy-pasting hooks between repos like it's 2005.

## 🎯 How It Works

When you trigger any Git hook (like `pre-commit`), here's what happens behind the scenes:

1. **global hooks run first** → Executes all scripts in `./hooks/pre-commit.d/*`
2. **Repo-specific hooks follow** → Then runs any hooks in `$YOUR_REPO/.git/hooks/pre-commit.d/*`
3. **Default hook runs last** → `$YOUR_REPO/.git/hooks/pre-commit`
3. **Fail fast** → If any hook returns non-zero, the chain stops immediately
4. **Deterministic order** → Scripts run in alphabetical order (use `01-`, `02-` prefixes for ordering)

All your existing hooks still work with no changes.

## 🚀 Installation

Run the setup script and you're good to go:

```bash
./setup
# => creates hooks and sets gitconfig core.hooksPath
```

## ✨ Adding Your Own Hooks

Want to add a new hook? Easy peasy:

```bash
# Create the hook directory (if it doesn't exist already)
mkdir -p hooks/pre-commit.d

# Drop your script in there
cp your-awesome-script.sh hooks/pre-commit.d/

# Make it executable
chmod +x hooks/pre-commit.d/your-awesome-script.sh
```

Add repo-specific git hooks too (e.g. copy to `$YOUR-REPO/.git/hooks/pre-commit.d/`)


**Deterministic Ordering** Name scripts with number prefixes to guaranteed ordering:
- `01-lint.sh` → Runs first
- `02-format.sh` → Runs second
- `99-final-check.sh` → Runs last

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
VERBOSE=1 git commit -m "debugging time!"
```

Shows you which hooks are running in what order.

## 🚫 Disabling Hooks

### 🤔 Skip hooks for one command ("I know what I'm doing" mode)
```bash
git -c core.hooksPath= commit -m "living dangerously"
```

### ☢️ Turn off globally (nuclear option)
```bash
git config --global --unset core.hooksPath
```

## 🎁 Bonus: Example Hook Included!

Check out `hooks/pre-commit.d/end-with-blank-line` - it automatically adds blank lines to the end of your source files. Because nobody likes that "No newline at end of file" message!

## 🤝 Contributing

Found a bug? Have an awesome hook to share? PRs are welcome! Let's make Git hooks great again!

# 🪪 License

Apache License, Version 2.0

git-multi-hook Copyright © 2025 Patrick Wyatt

See [LICENSE.md](LICENSE.md) for details.


# 😻 Thanks to

Thanks to [Simon Epskamp](https://stackoverflow.com/users/146738/simon-epskamp) based on this [stackoverflow post](https://stackoverflow.com/a/61341619).
