# Vite + React + Tailwind + Biome Starter

fast, opinionated frontend starter kit featuring **Vite 7**, **React 19**, **TailwindCSS 4**, and **Biome** for all your linting/formatting needs.

---

## Features

- **Vite** – Lightning-fast dev server & bundler
- **React 19** – Latest stable React
- **TailwindCSS 4** – Utility-first CSS framework
- **Biome** – Linting + formatting (no ESLint/Prettier needed!)
- **pnpm** – Fast package manager
- **Opinionated configs**:
  - Tabs for indentation
  - Double quotes in JS/TS
  - Auto-sorted Tailwind classes (`className` & `cn()`)

---

## Setup

### 1. Clone Template

```bash
git clone git@github.com:chandrayan0417/vite-template.git my-app
cd my-app
pnpm install
```

_or, if you’ve set up the function:_

```bash
create-vite my-app
```

### 2. Start Dev Server

```bash
pnpm dev
```
Runs at [http://localhost:5173](http://localhost:5173)  
Accessible via LAN (thanks to `--host`).

---

## Scripts Reference

| Script        | Description                          |
|---------------|--------------------------------------|
| pnpm dev      | Start dev server (LAN accessible)    |
| pnpm build    | Build production bundle              |
| pnpm preview  | Preview built app locally            |
| pnpm lint     | Run Biome linter                     |
| pnpm format   | Auto-fix & format with Biome         |

---

## Neovim + tmux Workflow

Instantly create a full dev workspace with Neovim + tmux!  
This script sets up 3 windows: editor, dev server, and an extra shell.

### Setup

1. **Copy the Script Below:**
    Save as `tmux-nvim.sh`.

    ```bash
    #!/bin/bash

    # Use current directory name as session name
    SESSION=$(basename "$PWD")
    PROJECT_DIR="$PWD"

    # Check if session already exists
    if tmux has-session -t "$SESSION" 2>/dev/null; then
      echo "Tmux session '$SESSION' already exists."
      read -p "Do you want to kill and recreate it? (y/N): " confirm
      case "$confirm" in
      [yY][eE][sS] | [yY])
        tmux kill-session -t "$SESSION"
        ;;
      *)
        echo "✔️  Attaching to existing session '$SESSION'..."
        tmux attach -t "$SESSION"
        exit 0
        ;;
      esac
    fi

    # Create new tmux session
    tmux new-session -d -s "$SESSION" -c "$PROJECT_DIR"

    # Window 1: Editor
    tmux rename-window -t "$SESSION:0" 'nvim'
    tmux send-keys -t "$SESSION:0" 'nvim .' C-m

    # Window 2: Dev + Backend
    tmux new-window -t "$SESSION:1" -n 'server' -c "$PROJECT_DIR"
    tmux send-keys -t "$SESSION:1" 'pnpm dev' C-m
    tmux split-window -h -t "$SESSION:1" -c "$PROJECT_DIR"
    tmux send-keys -t "$SESSION:1.1" 'clear' C-m

    # Window 3: Extra
    tmux new-window -t "$SESSION:2" -n 'extra' -c "$PROJECT_DIR"
    tmux send-keys -t "$SESSION:2" 'clear' C-m

    # Focus back to editor window
    tmux select-window -t "$SESSION:0"
    tmux attach -t "$SESSION"
    ```

2. **Make the script executable:**

    ```bash
    chmod +x tmux-nvim.sh
    ```

3. **Run it inside your project folder:**

    ```bash
    ./tmux-nvim.sh
    ```

---

## Optional: Add Functions to Your `.zshrc`

Make your dev life even easier by adding these shortcuts in your `~/.zshrc`!

### 1. Open your `.zshrc` file

```bash
nvim ~/.zshrc
```

### 2. Add These Functions

```bash
create-vite() {
  if [ -z "$1" ]; then
    echo "Please provide a project name"
    return 1
  fi

  PROJECT_NAME=$1
  GITHUB_REPO="git@github.com:chandrayan0417/vite-template.git"

  # Clone into new folder
  git clone "$GITHUB_REPO" "$PROJECT_NAME" || return 1

  # Go inside project
  cd "$PROJECT_NAME" || return 1

  # Optional: remove old git history so it's a fresh project
  rm -rf .git
  git init -b main
  git add .
  git commit -m "Initial commit from vite-template"

  # Install dependencies
  pnpm install

  # Open nvim and start server
  wdev
}
```

- **Bonus:** You can also add `wdev` alias for running `tmux-nvim.sh` easily!

```bash
alias wdev='./tmux-nvim.sh'
```

### 3. Reload Your Shell

```bash
source ~/.zshrc
```

### 4. Usage

- Quickly scaffold a new project:
    ```bash
    create-vite my-app
    ```
- Instantly start your tmux+neovim dev environment:
    ```bash
    wdev
    ```

---

- Opens your editor, server, and extra window in tmux, auto-attaches, auto-handles existing session.  
- Includes handy `create-vite` function for instant new projects!

---

## Notes

- Git integration is disabled by default in `.biome.json` (`vcs.enabled: false`)
- Tailwind classes are auto-sorted (Biome `nursery/useSortedClasses`)
- Ready for TypeScript, even if you start with plain React
