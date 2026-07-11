# OpenMandriva Linux Overview

OpenMandriva (officially OpenMandriva Lx) is a community-driven Linux distribution and a direct descendant of the once highly popular Mandriva Linux (formerly Mandrake Linux). This document provides a comprehensive overview of OpenMandriva's unique features, tools, and package management ecosystem.

## 1. History & Lineage

- **Roots in Mandrake/Mandriva:** Mandrake Linux was renowned in the late 1990s and early 2000s for its user-friendly approach, featuring graphical installers and an intuitive control center. After Mandriva S.A. faced financial difficulties and bankruptcy, the community stepped in to continue the legacy.
- **Formation of the Association:** The OpenMandriva Association was established in 2012 to maintain and develop the distribution, transitioning to a fully community-led model while preserving Mandriva's spirit.
- **ROSA Linux Influence:** Initially based on ROSA Linux (a Russian Mandriva fork), OpenMandriva has since evolved into an independent project with its own unique direction.

## 2. Key Technical Features

- **LLVM/Clang Compiled:** OpenMandriva's most distinguishing feature is its use of the LLVM/Clang toolchain to compile the entire system, including the Linux kernel. This contrasts with most distributions that use GCC, offering faster compilation times, different performance characteristics, and serving as a proving ground for Clang in a full OS environment.
- **Desktop Environment:** The flagship desktop is **KDE Plasma**, providing a highly customized, polished experience. Community spins for GNOME, LXQt, and XFCE are also available.
- **Package Management:** Uses RPM packages with `dnf` as the primary package manager, replacing the legacy `urpmi`.
- **Release Models:**
  - **Rock:** Traditional stable point-release with security and bug-fix updates.
  - **ROME:** Rolling release edition offering bleeding-edge software and continuous updates.

## 3. Signature Tools

- **OM Welcome:** A user-friendly welcome screen that helps configure the system, install proprietary software (multimedia codecs, drivers), and introduces users to the distribution.
- **OM Control Center:** Inherits Mandrake's legacy with GUI tools for managing system settings, hardware, networks, and software.
- **Desktop Presets (om-feeling-like):** A tool that allows users to quickly change desktop layout to mimic Windows, macOS, Ubuntu, or traditional Plasma.
- **dnfdragora:** A graphical package management interface for DNF, providing intuitive browsing, installation, and removal of software packages.

## 4. Package Management Ecosystem

OpenMandriva provides a flexible, layered approach to software management through three complementary package managers:

### 4.1 DNF Package Manager (System Core)
DNF (Dandified YUM) is the primary package manager for system-level tools, libraries, and core applications. It manages RPM packages with advanced dependency resolution using a SAT solver (hawkey/libsolv).

**Common Operations:**
- System update: `sudo dnf upgrade`
- Install package: `sudo dnf install <package>`
- Remove package: `sudo dnf remove <package>`
- Search packages: `dnf search <keyword>`
- Package info: `dnf info <package>`
- Find provider: `dnf provides <file_or_command>`

**Repository Management:**
- List repos: `dnf repolist`
- Enable repo: `sudo dnf config-manager --set-enabled <repo>`
- Disable repo: `sudo dnf config-manager --set-disabled <repo>`

**Advanced Features:**
- Transaction history and rollbacks: `dnf history` and `sudo dnf history undo <ID>`
- Module streams for multiple software versions (e.g., Node.js 16/18)

### 4.2 Deep Dive: Flatpak

Flatpak is the recommended way to install **desktop applications** on OpenMandriva. It packages apps with all their dependencies in a sandboxed container, so they never conflict with your system packages or each other.

#### What Is Flatpak?

Flatpak is a **distribution-agnostic application packaging system**. Instead of relying on the host system's libraries (like DNF does), each Flatpak app bundles its own dependencies inside a sandbox. This means:

- An app compiled for Flatpak works on **any** Linux distro — OpenMandriva, Fedora, Arch, Ubuntu, all the same.
- Apps are **sandboxed** — they can't access your system files unless you explicitly grant permission.
- Updates are **independent** of your distro's release cycle — you get app updates as soon as developers ship them.

#### Architecture

```
┌─────────────────────────────────────────────────┐
│              Flatpak Application                 │
│   ┌──────────────────────────────────────────┐   │
│   │          App Content (your app)          │   │
│   ├──────────────────────────────────────────┤   │
│   │       Runtime (shared base libs)        │   │
│   │  org.freedesktop.Platform  (base)       │   │
│   │  org.kde.Platform          (KDE apps)   │   │
│   │  org.gnome.Platform        (GNOME apps) │   │
│   ├──────────────────────────────────────────┤   │
│   │       Sandbox (bubblewrap)              │   │
│   │  - Isolated filesystem view             │   │
│   │  - Controlled portal access             │   │
│   │  - Explicit permissions                 │   │
│   └──────────────────────────────────────────┘   │
├─────────────────────────────────────────────────┤
│              Flatpak Runtime (host)              │
│   - flatpak daemon (flatpak-system-daemon)      │
│   - OSTree (content-addressable storage)         │
│   - Bubblewrap (sandboxing)                      │
└─────────────────────────────────────────────────┘
```

**Key concepts:**
- **Runtimes** — Shared base libraries that multiple apps can use. Instead of each app bundling `glibc`, `mesa`, etc., they share a common runtime. This saves disk space and memory.
- **Remotes** — Repositories where Flatpak apps are hosted. The main one is **Flathub** (flathub.org), which has thousands of apps.
- **Sandbox** — Each app runs in an isolated environment. It can only see its own files and anything you explicitly expose to it.

#### Installation & Setup

OpenMandriva does not ship Flatpak pre-installed. You need to set it up:

```bash
# 1. Install the flatpak package
sudo dnf install flatpak

# 2. Add Flathub repository (the main app store for Flatpak)
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo

# 3. Verify setup
flatpak remotes
flatpak search hello
```

That's it. You can now install any app from Flathub.

#### Core Commands

**Installing apps:**
```bash
# Install an app (by app ID from Flathub)
flatpak install flathub org.mozilla.firefox

# Install a specific version/branch
flatpak install flathub org.mozilla.firefox stable

# Install without confirmation prompts (for scripting)
flatpak install -y flathub org.mozilla.firefox

# Install from a local .flatpak file
flatpak install ~/Downloads/myapp.flatpak
```

**Running apps:**
```bash
# Run an app
flatpak run org.mozilla.firefox

# Run with specific options
flatpak run org.mozilla.firefox --private-window

# Run a command inside the app's sandbox
flatpak run --command=bash org.mozilla.firefox

# List available .desktop files for an app
flatpak info --show-metadata org.mozilla.firefox
```

**Updating apps:**
```bash
# Update all installed apps
flatpak update

# Update a specific app
flatpak update org.mozilla.firefox

# Check for updates without installing
flatpak update --dry-run
```

**Removing apps:**
```bash
# Uninstall an app
flatpak uninstall org.mozilla.firefox

# Uninstall and remove unused runtimes
flatpak uninstall --unused

# Remove app data (settings, cache)
rm -rf ~/.var/app/org.mozilla.firefox
```

**Querying:**
```bash
# List all installed apps
flatpak list

# List only apps (no runtimes)
flatpak list --app

# List installed remotes
flatpak remotes

# Search for an app
flatpak search <keyword>

# Get detailed info about an app
flatpak info org.mozilla.firefox

# Show what permissions an app has
flatpak info --show-permissions org.mozilla.firefox
```

#### Managing Remotes (Repositories)

```bash
# Add a remote
flatpak remote-add --user myrepo https://example.com/repo.flatpakrepo

# Add a remote from a local file
flatpak remote-add --user myrepo /path/to/repo.flatpakrepo

# Remove a remote
flatpak remote-delete myrepo

# Disable a remote (keep it but stop installing from it)
flatpak remote-modify myrepo --disable

# Re-enable a remote
flatpak remote-modify myrepo --enable
```

#### Sandboxing & Permissions

Flatpak apps run sandboxed by default. You can inspect and modify permissions:

```bash
# See what an app can access
flatpak info --show-permissions org.mozilla.firefox

# Override permissions at install time
flatpak install --user --no-interaction flathub org.mozilla.firefox

# Override permissions post-install using Flatseal (GUI tool)
# Or manually edit overrides:
flatpak permission-set filesystem host org.mozilla.firefox
```

Common permission overrides:
```bash
# Grant access to a specific directory
flatpak permission-set filesystem home org.mozilla.firefox

# Grant GPU acceleration (usually auto-detected)
flatpak permission-set device dri org.mozilla.firefox

# Grant network access (usually default)
flatpak permission-set socket network org.mozilla.firefox
```

#### Cleaning Up

Flatpak apps and runtimes accumulate over time. Clean them:

```bash
# Remove unused runtimes and extensions
flatpak uninstall --unused

# Show disk usage by Flatpak
du -sh ~/.local/share/flatpak
du -sh /var/lib/flatpak

# Remove all Flatpak data (nuclear option)
sudo rm -rf /var/lib/flatpak/*
rm -rf ~/.local/share/flatpak/*
```

#### Flatpak vs DNF for Desktop Apps

| Aspect | Flatpak | DNF |
|---|---|---|
| Sandboxing | Yes, by default | No |
| Update speed | Immediate (Flathub) | Tied to repo updates |
| Disk usage | Higher (bundled deps) | Lower (shared system libs) |
| Dependency conflicts | Impossible | Possible |
| System integration | Slightly less native | Fully native |
| Best for | Third-party GUI apps | System packages, core tools |

**Rule of thumb:** Use **Flatpak for desktop apps** (Firefox, VS Code, Steam, GIMP) and **DNF for system packages** (kernel, drivers, libraries, CLI tools).

#### Common Flatpak App IDs

| App | Flatpak ID |
|---|---|
| Firefox | `org.mozilla.firefox` |
| VS Code | `com.visualstudio.code` |
| Steam | `com.valvesoftware.Steam` |
| GIMP | `org.gimp.GIMP` |
| VLC | `org.videolan.VLC` |
| LibreOffice | `org.libreoffice.LibreOffice` |
| Spotify | `com.spotify.Client` |
| Discord | `com.discordapp.Discord` |
| OBS Studio | `com.obsproject.Studio` |
| 1Password | `com.onepassword.OnePassword` |

Browse the full catalog at **flathub.org**.

---

### 4.3 Deep Dive: Homebrew (Linuxbrew)

Homebrew is the recommended way to install **CLI tools and developer utilities** on OpenMandriva. Originally for macOS, the Linux port ("Linuxbrew") installs packages entirely in user space — no root required.

#### What Is Homebrew?

Homebrew is a **user-space package manager** that installs software into `/home/linuxbrew/.linuxbrew/` instead of `/usr/`. This means:

- You never need `sudo` to install or update packages.
- It **never touches** your system packages managed by DNF.
- It's great for getting **newer versions** of tools than what OpenMandriva's repos provide.
- It works almost identically to Homebrew on macOS, so your muscle memory transfers.

#### Architecture

```
┌─────────────────────────────────────────────────┐
│              User Space                          │
│   /home/linuxbrew/.linuxbrew/                    │
│   ├── Cellar/          (installed packages)      │
│   │   ├── node@20.11.0/                         │
│   │   ├── python@3.12.0/                        │
│   │   ├── ripgrep/                               │
│   │   └── ...                                    │
│   ├── opt/            (symlinks to Cellar)       │
│   ├── lib/             (shared libraries)         │
│   ├── bin/              (executables)             │
│   └── etc/             (config files)             │
├─────────────────────────────────────────────────┤
│              Homebrew Core (GitHub)               │
│   - Formulae: build scripts for CLI tools        │
│   - Casks: installers for GUI apps (macOS only)  │
│   - Taps: third-party repositories               │
└─────────────────────────────────────────────────┘
```

**Key concepts:**
- **Formulae** — Build scripts (Ruby) that compile and install a package from source or pre-built binaries.
- **Bottles** — Pre-compiled binaries so you don't have to compile from source.
- **Cellar** — Where installed packages actually live (`/home/linuxbrew/.linuxbrew/Cellar/`).
- **Taps** — Third-party repositories you can add (like PPAs for apt).

#### Installation & Setup

```bash
# 1. Install prerequisites
sudo dnf install -y gcc git curl make

# 2. Install Homebrew (the official script)
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 3. Add Homebrew to your PATH
#    The installer will tell you exactly what to add, but typically:
echo 'eval "$(/home/linuxbrew/.linuxbrew/bin/brew shellenv)"' >> ~/.bashrc
source ~/.bashrc

# 4. Verify
brew --version
brew doctor
```

`brew doctor` checks your setup and warns about potential issues.

#### Core Commands

**Installing packages:**
```bash
# Install a package
brew install ripgrep

# Install a specific version
brew install node@20

# Install multiple packages at once
brew install git curl wget htop

# Install without cleanup (faster)
HOMEBREW_NO_INSTALL_CLEANUP=1 brew install <package>
```

**Searching:**
```bash
# Search for a package
brew search <keyword>

# Search formulae only (CLI tools)
brew search --formula <keyword>

# Search casks (GUI apps — macOS only, limited on Linux)
brew search --cask <keyword>

# Get info about a package
brew info <package>

# See what files a package installed
brew list --verbose <package>
```

**Updating & upgrading:**
```bash
# Update Homebrew itself (the formula index)
brew update

# Upgrade all installed packages
brew upgrade

# Upgrade a specific package
brew upgrade <package>

# Check what's outdated
brew outdated
```

**Removing packages:**
```bash
# Uninstall a package
brew uninstall <package>

# Uninstall and remove dependencies no longer needed
brew uninstall --ignore-dependencies <package>

# Remove old versions of all packages
brew cleanup

# Remove specific old version
brew cleanup <package>
```

**Querying & info:**
```bash
# List all installed packages
brew list

# List installed formulae only
brew list --formula

# Show dependencies of a package
brew deps <package>

# Show which packages depend on a given package
brew uses --installed <package>

# Check if a package is installed
brew list <package> &>/dev/null && echo "installed" || echo "not installed"
```

#### Managing Taps (Third-Party Repos)

```bash
# Add a tap
brew tap <user/repo>

# List taps
brew tap

# Remove a tap
brew untap <user/repo>
```

#### Homebrew on Linux vs macOS

| Feature | Linuxbrew | macOS Homebrew |
|---|---|---|
| Install location | `/home/linuxbrew/.linuxbrew/` | `/opt/homebrew/` (Apple Silicon) or `/usr/local/` (Intel) |
| Casks (GUI apps) | Very limited | Full support |
| Root required | Never | Never |
| System integration | Slightly less native | Fully native |
| Best for | CLI tools, dev utilities | Everything |

**Note:** Homebrew Casks (which install `.app` bundles on macOS) have **very limited support on Linux**. For GUI apps on Linux, use **Flatpak** instead.

#### When to Use Homebrew vs DNF

| Scenario | Use Homebrew | Use DNF |
|---|---|---|
| Latest version of a CLI tool | Yes | Sometimes older |
| System-level packages | Never | Always |
| Kernel, drivers, firmware | Never | Always |
| Development tools (git, node, python) | Yes (newer versions) | Yes (system versions) |
| Root access not available | Yes | No |
| Need specific version of a tool | Yes (e.g., `node@20`) | Limited |

**Rule of thumb:** Use **Homebrew for CLI tools and dev utilities** and **DNF for system packages**.

#### Recommended Homebrew Packages for OpenMandriva

```bash
# Modern CLI replacements
brew install ripgrep          # grep replacement (rg)
brew install fd                # find replacement
brew install bat               # cat replacement
brew install eza               # ls replacement
brew install fzf               # fuzzy finder
brew install zoxide            # cd replacement (smart)
brew install starship          # cross-shell prompt

# Development tools
brew install node              # Node.js
brew install python@3.12       # Python (alternative to system python)
brew install go                # Go
brew install rust              # Rust (via rustup, not brew)

# Utilities
brew install htop              # Process monitor
brew install tldr              # Simplified man pages
brew install jq                # JSON processor
brew install tree              # Directory tree viewer
brew install wget              # HTTP downloader
brew install tmux              # Terminal multiplexer
brew install neovim            # Modern Vim
```

#### Keeping Homebrew Healthy

```bash
# Regular maintenance
brew update && brew upgrade && brew cleanup

# Check for issues
brew doctor

# Check what's taking space
du -sh /home/linuxbrew/.linuxbrew/

# Remove everything (nuclear option)
# !!!! DANGER: This removes ALL brew-installed packages !!!!
rm -rf /home/linuxbrew/.linuxbrew
```

---

### 4.4 How the Three Package Managers Coexist

OpenMandriva's triple-package-manager strategy works because each tool operates in its own lane:

```
┌─────────────────────────────────────────────────────────┐
│                    Your System                           │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │  DNF (/usr)                                       │  │
│  │  Kernel, drivers, system libs, base packages       │  │
│  │  Managed by: root (sudo dnf)                       │  │
│  │  Packages: .rpm                                    │  │
│  └───────────────────────────────────────────────────┘  │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │  Flatpak (/var/lib/flatpak + ~/.local/share)      │  │
│  │  Desktop apps (Firefox, VS Code, Steam, GIMP)      │  │
│  │  Managed by: user (flatpak install)                │  │
│  │  Packages: sandboxed containers                    │  │
│  └───────────────────────────────────────────────────┘  │
│                                                         │
│  ┌───────────────────────────────────────────────────┐  │
│  │  Homebrew (/home/linuxbrew/.linuxbrew)             │  │
│  │  CLI tools, dev utilities (ripgrep, node, fzf)     │  │
│  │  Managed by: user (brew install, no sudo)          │  │
│  │  Packages: compiled from source or bottles         │  │
│  └───────────────────────────────────────────────────┘  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**No conflicts.** Each tool installs to its own prefix:
- DNF → `/usr/`
- Flatpak → `/var/lib/flatpak/` (system) or `~/.local/share/flatpak/` (user)
- Homebrew → `/home/linuxbrew/.linuxbrew/`

**PATH order matters.** Homebrew's bin directory should be **first** in your PATH so its tools take precedence over DNF's older versions:

```bash
# This is what brew shellenv does:
export PATH="/home/linuxbrew/.linuxbrew/bin:$PATH"
export MANPATH="/home/linuxbrew/.linuxbrew/share/man:$MANPATH"
export INFOPATH="/home/linuxbrew/.linuxbrew/share/info:$INFOPATH"
```

**The workflow:**
1. `sudo dnf install` — system packages (kernel updates, drivers, system libs)
2. `flatpak install` — desktop apps (browsers, editors, games)
3. `brew install` — CLI tools (the latest version of ripgrep, node, etc.)

This gives you the stability of a traditional distro, the app isolation of Flatpak, and the bleeding-edge CLI access of Homebrew — all without them interfering with each other.

### 4.5 Deep Dive: GUI Package Management Tools

OpenMandriva provides several graphical interfaces for package management. These tools wrap DNF, Flatpak, and Homebrew behind point-and-click UIs, making software installation accessible to users who prefer not to use the command line.

---

#### dnfdragora

dnfdragora is the **primary GUI package manager** for OpenMandriva. It's a Qt-based frontend for DNF that provides a traditional "Add/Remove Software" experience — similar to what Mandrake's `rpmdrake` used to be.

**What it does:**
- Browse, search, install, and remove RPM packages via DNF
- View package details, descriptions, screenshots, and changelogs
- See transaction previews before committing (what will be installed/removed)
- Manage repositories (enable/disable repos from the GUI)
- View and undo past transactions (wraps DNF history)
- Filter by status (installed, available, upgradable)

**Installation:**
```bash
sudo dnf install dnfdragora
```

dnfdragora comes in two variants:
- **dnfdragora** — The full Qt-based GUI (recommended for KDE Plasma)
- **dnfdragora-gui** — A simpler GTK-based variant for GNOME/XFCE spins

**Launching:**
```bash
# From terminal
dnfdragora

# Or find it in the application menu under "System" or "Administration"
```

**Key features:**

| Feature | Description |
|---|---|
| Package browser | Scrollable list with search, filters, and category grouping |
| Search | Real-time search across package names and descriptions |
| Package info | Shows version, size, dependencies, file list, changelog |
| Transaction preview | Shows exactly what will be installed/removed before confirming |
| Repository manager | Enable/disable repos from the GUI settings |
| History viewer | Browse past transactions and undo them |
| Grouping | Browse packages by category (System, Editors, Multimedia, etc.) |

**dnfdragora vs DNF CLI:**

| Aspect | dnfdragora | `dnf` CLI |
|---|---|---|
| Speed | Slower (GUI overhead) | Faster |
| Discoverability | Easy (browse categories) | Requires knowing package names |
| Scriptable | No | Yes |
| Transaction preview | Visual, detailed | Text-based |
| History rollback | GUI button | `sudo dnf history undo <ID>` |
| Best for | Casual users, browsing | Power users, scripting, automation |

**Tips:**
- Use the **Filter** dropdown to show only installed packages, only available packages, or only upgradable ones.
- The **Group** view organizes packages by category (like Synaptic's sections).
- Right-click a package for context actions (install, remove, info, changelog).
- dnfdragora uses a **batched transaction** model — you mark multiple packages for install/remove, then commit them all at once.

---

#### KDE Discover

KDE Discover is the **default software center** for KDE Plasma. It's the app store experience — curated, with ratings, screenshots, and automatic updates.

**What it does:**
- Browse and install applications from multiple sources (DNF, Flatpak, AppImages)
- View app ratings, reviews, screenshots, and descriptions
- Automatic background updates for installed apps
- System integration (appears in the Plasma task manager for updates)
- Manage Flatpak apps alongside DNF packages

**Installation:**
KDE Discover comes pre-installed on OpenMandriva KDE Plasma editions. If missing:
```bash
sudo dnf install plasma-discover
```

**Key features:**

| Feature | Description |
|---|---|
| Multi-source | Shows apps from DNF repos and Flathub in one place |
| Ratings & reviews | Community ratings with user reviews |
| Screenshots | Visual preview before installing |
| Auto-updates | Background update notifications in the system tray |
| Category browsing | Featured apps, categories, new arrivals |
| Flatpak integration | Install Flatpak apps directly from Discover |
| Source indicator | Shows whether an app is from DNF or Flatpak |

**Discover vs dnfdragora:**

| Aspect | KDE Discover | dnfdragora |
|---|---|---|
| Focus | End-user apps (curated) | System packages (all) |
| Sources | DNF + Flatpak + AppImage | DNF only |
| UX | App-store style (screenshots, ratings) | Traditional package manager style |
| Updates | Automatic, background | Manual |
| Best for | Installing desktop apps | Managing system packages |

**Rule of thumb:** Use **KDE Discover** to find and install apps (like a phone app store). Use **dnfdragora** when you need to manage system packages, dependencies, or repos.

---

#### GNOME Software

GNOME Software is the equivalent of KDE Discover for GNOME desktop spins. It provides a similar app-store experience with Flatpak and DNF integration.

**Installation:**
GNOME Software comes pre-installed on GNOME spins. If missing:
```bash
sudo dnf install gnome-software
```

**Key features:**
- App-store style browsing with categories and featured apps
- Flatpak support (search and install from Flathub)
- DNF backend for system packages
- Automatic updates via GNOME Software's update notifier
- Ratings and reviews from Flathub

**GNOME Software vs KDE Discover:**

| Aspect | GNOME Software | KDE Discover |
|---|---|---|
| Desktop | GNOME | KDE Plasma |
| Backend | PackageKit + Flatpak | DNF + Flatpak |
| UI toolkit | GTK (libadwaita) | Qt (Kirigami) |
| Flatpak support | Yes | Yes |
| Feature parity | Slightly fewer features | More polished on KDE |

---

#### PackageKit

PackageKit is a **system service** that provides a D-Bus API for package management. It's not a GUI itself — it's the **backend** that several GUI tools use to communicate with DNF.

**How it fits:**
```
┌─────────────────────────────────────┐
│         GUI Applications             │
│  GNOME Software, Apper, etc.        │
├─────────────────────────────────────┤
│         PackageKit (D-Bus API)       │
│  Provides: search, install, remove   │
│  via a standardized interface        │
├─────────────────────────────────────┤
│         DNF / RPM                    │
│  Actual package operations           │
└─────────────────────────────────────┘
```

**Why it exists:**
- Provides a **distro-agnostic API** — GNOME Software works on Fedora, OpenMandriva, and Debian (via different backends) without changing its code.
- Handles **polkit authentication** — prompts for sudo password via GUI when needed.
- Manages **caching and transactions** — batches operations for efficiency.

**Note:** dnfdragora does **not** use PackageKit — it talks to DNF directly via its Python API. KDE Discover also uses DNF's native interface, not PackageKit.

---

#### Apper (Legacy)

Apper is an older KDE-integrated package manager that uses PackageKit as its backend. It was popular in the Mandriva/ROSA era but is now considered **legacy**.

**Status:** Still available in repos but not actively developed. KDE Discover has replaced it as the default software center.

```bash
sudo dnf install apper
```

---

#### Comparison Matrix

| Tool | Type | Backend | Sources | UX Style | Active? |
|---|---|---|---|---|---|
| dnfdragora | Qt GUI | DNF (Python API) | RPM only | Traditional PM | Yes |
| KDE Discover | Qt App Store | DNF + Flatpak | RPM + Flatpak + AppImage | App store | Yes |
| GNOME Software | GTK App Store | PackageKit + Flatpak | RPM + Flatpak | App store | Yes |
| PackageKit | D-Bus service | DNF/RPM | RPM | Backend only | Yes |
| Apper | KDE GUI | PackageKit | RPM | Traditional PM | Legacy |
| OM Control Center | Qt GUI | DNF | RPM | Mandrake-style | Yes |

---

#### Recommended Workflow

```
┌──────────────────────────────────────────────────────────┐
│                  What do you want to do?                   │
├──────────────────────────────────────────────────────────┤
│                                                          │
│  "Install Firefox"                                        │
│  → KDE Discover (search "Firefox", click Install)         │
│  → or: flatpak install flathub org.mozilla.firefox        │
│                                                          │
│  "Install ripgrep"                                        │
│  → brew install ripgrep                                   │
│  → or: sudo dnf install ripgrep                           │
│                                                          │
│  "Update system"                                          │
│  → sudo dnf upgrade                                      │
│  → or: dnfdragora → Actions → System Upgrade             │
│                                                          │
│  "What's installed?"                                      │
│  → dnf list installed                                    │
│  → or: dnfdragora → Filter → Installed                   │
│                                                          │
│  "Enable a repo"                                         │
│  → sudo dnf config-manager --set-enabled <repo>          │
│  → or: dnfdragora → Settings → Repositories              │
│                                                          │
│  "Rollback a bad update"                                 │
│  → sudo dnf history undo <ID>                            │
│  → or: dnfdragora → History → Select → Undo              │
│                                                          │
└──────────────────────────────────────────────────────────┘
```

### 4.6 Integration Strategy
- **DNF:** Manages system core (kernel, libraries, base packages)
- **Flatpak:** Manages desktop applications (GUI apps with sandboxing)
- **Homebrew:** Manages developer tools (CLI utilities in user space)
- **GUI Tools:** Provide user-friendly interfaces for DNF and Flatpak operations

This layered approach provides flexibility while maintaining system stability.

## 5. Desktop Environments & Window Managers

OpenMandriva ships with **official spins** for several desktop environments and supports many more through its package repositories. You can also install standalone window managers for a minimal, keyboard-driven setup.

### 5.1 Official Desktop Spins

OpenMandriva produces **official ISO images** for these desktop environments. Each is a complete, curated installation experience:

| Desktop | Status | Package | Notes |
|---|---|---|---|
| **KDE Plasma** | Flagship, primary | `plasma-desktop` | Most polished, deeply customized. Recommended for new users. |
| **GNOME** | Official spin | `gnome-shell` | Modern, touch-friendly. Uses GTK, GNOME Settings. |
| **LXQt** | Official spin | `lxqt-desktop` | Lightweight Qt-based DE. Good for older hardware. |
| **XFCE** | Official spin | `xfce4-desktop` | Lightweight GTK-based DE. Traditional panel layout. |
| **MATE** | Community | `mate-desktop` | GNOME 2 fork. Traditional desktop metaphor. |
| **Cinnamon** | Community | `cinnamon-desktop` | Linux Mint's DE. Windows-like layout. |
| **Budgie** | Community | `budgie-desktop` | Modern, minimal. Originally from Solus. |

**Which official spin should you pick?**

```
┌─────────────────────────────────────────────────────────────┐
│                  Which desktop is right for you?             │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  "I want the best experience"                               │
│  → KDE Plasma (flagship, most polished)                     │
│                                                             │
│  "I want modern and touch-friendly"                         │
│  → GNOME                                                    │
│                                                             │
│  "I have old/slow hardware"                                 │
│  → LXQt (lightest) or XFCE (light + traditional)           │
│                                                             │
│  "I want a traditional desktop (like Windows XP/7)"         │
│  → MATE or Cinnamon                                         │
│                                                             │
│  "I want minimal and clean"                                 │
│  → Budgie or install a window manager directly              │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### 5.2 Installing Alternative Desktops

If you installed one spin but want to try another, you can install additional DEs alongside your current one. The login screen (SDDM on KDE, GDM on GNOME) will let you choose which session to launch.

```bash
# Install GNOME on a KDE system
sudo dnf group install "GNOME Desktop Environment"

# Install KDE Plasma on a GNOME system
sudo dnf group install "KDE Plasma Desktop"

# Install LXQt
sudo dnf group install "LXQt Desktop"

# Install XFCE
sudo dnf group install "XFCE Desktop"

# Install MATE
sudo dnf group install "MATE Desktop"

# Install Cinnamon
sudo dnf group install "Cinnamon Desktop"
```

**After installing, log out** and select the new desktop from your display manager's session selector (gear icon on the login screen).

**Cleaning up unused DEs:**
```bash
# Remove a desktop environment you no longer want
sudo dnf group remove "GNOME Desktop Environment"

# Autoremove orphaned packages
sudo dnf autoremove
```

**Note:** Installing multiple DEs can add significant disk usage (hundreds of MBs) and may pull in conflicting themes, sound servers, or notification daemons. It's generally fine to have 2-3 DEs, but avoid installing all of them.

### 5.3 Window Managers

For users who want a **minimal, keyboard-driven, or tiling** setup, OpenMandriva provides window managers in its repositories. These are not full desktop environments — they're just the window manager (no file manager, settings panel, or app store included). You compose your own desktop.

#### Tiling Window Managers

Tiling WMs automatically arrange windows in non-overlapping tiles, maximizing screen real estate and reducing mouse usage.

| WM | Package | Style | Language |
|---|---|---|---|
| **i3** | `i3` | Manual tiling | C |
| **Sway** | `sway` | Manual tiling (Wayland) | C |
| **bspwm** | `bspwm` | Manual tiling (tree-based) | C |
| **dwm** | `dwm` | Dynamic tiling (suckless) | C |
| **Awesome** | `awesome` | Dynamic tiling + scripting | Lua |
| **Qtile** | `python-qtile` | Dynamic tiling + Python config | Python |
| **xmonad** | `xmonad` | Dynamic tiling | Haskell |
| **herbstluftwm** | `herbstluftwm` | Manual tiling | C |
| **dwm** | `dwm` | Dynamic (suckless) | C |

**Manual tiling** = you define layouts and move windows yourself.
**Dynamic tiling** = the WM automatically arranges windows according to rules.

#### Stacking / Floating Window Managers

These behave more like traditional desktops — windows can overlap and you move/resize them freely.

| WM | Package | Notes |
|---|---|---|
| **Openbox** | `openbox` | Lightweight, highly configurable. Classic Mandriva choice. |
| **Fluxbox** | `fluxbox` | Lightweight, fast. Good for old hardware. |
| **IceWM** | `icewm` | Very lightweight. Taskbar + menus. |
| **JWM** | `jwm` | Extremely lightweight. Used by Puppy Linux. |
| **FVWM** | `fvwm` | Powerful, highly customizable. Classic X11. |
| **Window Maker** | `windowmaker` | NeXTSTEP-style. Unique look. |

#### Wayland Compositors

Wayland is the modern replacement for X11. These compositors act as both window manager and display server:

| Compositor | Package | Style | Notes |
|---|---|---|---|
| **Sway** | `sway` | Tiling | i3-compatible, Wayland-native |
| **Hyprland** | `hyprland` | Dynamic tiling | Animated, modern, highly configurable |
| **River** | `river` | Manual tiling | Minimal, Zig-based |
| **Labwc** | `labwc` | Stacking | Openbox-like for Wayland |
| **Cage** | `cage` | Kiosk | Single-app kiosk mode |

**Note:** OpenMandriva uses **SDDM** as its display manager, which supports both X11 and Wayland sessions. When installing a Wayland compositor, you may need to select "Plasma (Wayland)" or the compositor name from the session selector.

### 5.4 Installing Window Managers

```bash
# i3 (most popular tiling WM)
sudo dnf install i3 dmenu i3status i3lock

# Sway (Wayland tiling, i3-compatible)
sudo dnf install sway waybar wofi mako

# Hyprland (animated tiling)
sudo dnf install hyprland xdg-desktop-portal-hyprland

# Openbox (lightweight stacking)
sudo dnf install openbox obconf tint2

# Awesome (dynamic tiling + Lua scripting)
sudo dnf install awesome

# Qtile (dynamic tiling + Python config)
sudo dnf install python-qtile
```

**Minimum packages for a usable setup:**

Each WM needs a few companion tools to be functional:

```
Window Manager
├── Display Manager    (SDDM, LightDM, or GDM — to log in)
├── Status Bar         (polybar, waybar, i3bar, tint2)
├── Application Launcher (dmenu, rofi, wofi, fuzzel)
├── Notification Daemon (dunst, mako, xfce4-notifyd)
├── Terminal Emulator   (kitty, alacritty, foot, xfce4-terminal)
├── Compositor          (picom, dwm's built-in, Sway's built-in)
└── File Manager        (thunar, pcmanfm, nemo — optional)
```

**Example: minimal i3 setup:**
```bash
sudo dnf install i3 dmenu i3status i3lock picom kitty xfce4-terminal thunar
```

**Example: minimal Sway setup (Wayland):**
```bash
sudo dnf install sway waybar wofi mako foot thunar
```

### 5.5 Display Managers

The display manager (DM) is the login screen. OpenMandriva uses SDDM by default, but you can switch:

| DM | Package | Desktop | Notes |
|---|---|---|---|
| **SDDM** | `sddm` | KDE Plasma (default) | Qt-based, modern, Wayland support |
| **GDM** | `gdm` | GNOME | GNOME's default DM |
| **LightDM** | `lightdm` | XFCE, MATE, etc. | Lightweight, GTK-based |
| **LXDM** | `lxdm` | LXQt | Very lightweight |

**Switching display managers:**
```bash
# Switch to LightDM
sudo dnf install lightdm lightdm-gtk
sudo systemctl disable sddm
sudo systemctl enable lightdm
sudo reboot

# Switch back to SDDM
sudo systemctl disable lightdm
sudo systemctl enable sddm
sudo reboot
```

### 5.6 Desktop Environment Feature Comparison

| Feature | KDE Plasma | GNOME | XFCE | LXQt | MATE |
|---|---|---|---|---|---|
| Toolkit | Qt | GTK4 | GTK2/3 | Qt | GTK2/3 |
| Customization | Very high | Limited (extensions) | High | High | High |
| Resource usage | ~400MB | ~500MB | ~250MB | ~200MB | ~300MB |
| Wayland support | Excellent | Native | Partial | No | No |
| Touch support | Good | Excellent | Limited | Limited | Limited |
| Panel/taskbar | Highly configurable | Dash (overview) | Traditional panel | Traditional panel | Traditional panel |
| File manager | Dolphin | Nautilus | Thunar | PCManFM-Qt | Caja |
| Settings | System Settings | GNOME Settings | Settings Manager | LXQt Settings | MATE Control Center |
| Animations | Yes (configurable) | Yes (limited) | No | No | No |
| HiDPI | Excellent | Excellent | Good | Good | Good |
| Extensions/plugins | KDE Store | GNOME Extensions | XFCE Plugins | Limited | MATE Applets |

### 5.7 Window Manager Feature Comparison

| Feature | i3 | Sway | bspwm | Awesome | Openbox | Hyprland |
|---|---|---|---|---|---|---|
| Display server | X11 | Wayland | X11 | X11 | X11 | Wayland |
| Tiling | Manual | Manual | Manual | Dynamic | Stacking | Dynamic |
| Config format | Text file | Text file | Shell script | Lua | XML | Text file |
| Multi-monitor | Yes | Yes | Yes | Yes | Yes | Yes |
| Gaps/borders | Yes (via config) | Yes (built-in) | Yes (built-in) | Yes (rules) | Yes (via picom) | Yes (built-in) |
| Status bar | i3bar | Waybar | Polybar | Built-in | Tint2 | Waybar |
| App launcher | dmenu/rofi | wofi/fuzzel | dmenu/rofi | Built-in | dmenu/rofi | wofi/fuzzel |
| Animations | No | No | No | Yes (limited) | No | Yes (smooth) |
| Floating mode | Yes | Yes | Yes | Yes | Default | Yes |
| Scripting | Shell | Shell | Shell | Lua | Shell | Python/Shell |
| Learning curve | Medium | Medium | Medium | High | Low | Low |

### 5.8 Recommended Setups

**For productivity (keyboard-driven):**
```
i3 + polybar + rofi + picom + kitty
# or
Sway + Waybar + wofi + foot
```

**For minimal/old hardware:**
```
Openbox + tint2 + dmenu + picom
# or
IceWM + default panel
```

**For modern look with animations:**
```
Hyprland + Waybar + wofi + foot
# or
KDE Plasma (Wayland)
```

**For maximum customization:**
```
Awesome WM (Lua scripting for everything)
# or
KDE Plasma (most configurable DE)
```

## 6. Deep Dive: DNF Package Manager

DNF represents a significant improvement over legacy package managers in the Mandriva lineage:

### Why DNF?
- **Advanced Dependency Resolution:** Uses SAT solver for mathematically sound dependency resolution, preventing system conflicts.
- **Memory Efficiency:** Significantly lower RAM usage compared to YUM.
- **Python API:** Extensible through plugins and integrates with GUI tools like OM Control Center and KDE Discover.

### Repository Structure
OpenMandriva organizes software into multiple repositories:
- **Main:** Core system packages
- **Unsupported:** Additional packages not officially supported
- **Restricted:** Proprietary or legally restricted software
- **Non-Free:** Commercial or non-open-source software

### Comparison with Other Package Managers
| Feature | DNF (OpenMandriva) | APT (Debian/Ubuntu) | Pacman (Arch) |
|---------|-------------------|---------------------|---------------|
| Package Format | RPM | DEB | PKG |
| Dependency Resolution | SAT solver | Advanced | Basic |
| Memory Usage | Low | Moderate | Low |
| Configuration | /etc/dnf/ | /etc/apt/ | /etc/pacman.conf |
| GUI Frontend | dnfdragora, KDE Discover | GNOME Software, Synaptic | Pamac, Octopi |

## 7. Summary

OpenMandriva bridges historical user-friendly Linux (Mandrake) with modern engineering (LLVM/Clang, rolling releases). It offers:
- **For KDE lovers:** A polished, customized Plasma experience
- **For developers:** LLVM/Clang ecosystem and rolling release option
- **For flexibility:** Three complementary package managers (DNF, Flatpak, Homebrew)
- **For user-friendly interfaces:** GUI tools like dnfdragora and KDE Discover
- **For stability:** Traditional Rock release or bleeding-edge ROME

Whether you're migrating from Debian/Ubuntu, Red Hat/Fedora, or seeking an alternative distribution, OpenMandriva provides a unique combination of legacy user-friendliness and modern innovation.