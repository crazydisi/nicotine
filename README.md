# Nicotine

High-performance EVE Online multiboxing tool for X11/Linux, inspired by EVE-O Preview.

## Features

- **Instant client cycling** with mouse buttons (forward/backward)
- **Always-on-top overlay** showing all clients and their status
- **Daemon architecture** for zero-latency window switching (~2ms vs ~50-100ms)
- **Auto-stack windows** to perfectly center multiple EVE clients
- **Draggable overlay** with middle-mouse button
- **Auto-detects display resolution** - works on any monitor setup

## Quick Install

### From GitHub Releases (Recommended)

```bash
curl -sSL https://raw.githubusercontent.com/USER/nicotine/main/install-github.sh | bash
```

### From Source

```bash
git clone https://github.com/USER/nicotine
cd nicotine
./install-local.sh
```

Then restart your terminal and run:
```bash
nicotine start    # Automatically runs in background
```

## Usage

### Basic Commands

```bash
nicotine start          # Start everything (daemon + overlay)
nicotine stack          # Stack all EVE windows
nicotine forward        # Cycle to next client
nicotine backward       # Cycle to previous client
```

### Mouse Bindings

Set up with xbindkeys (`~/.xbindkeysrc`):
```bash
# Forward cycle
"~/.local/bin/nicotine forward"
    b:9

# Backward cycle
"~/.local/bin/nicotine backward"
    b:8
```

Then run `xbindkeys` to activate.

### Overlay Controls

- **[R] Restack Windows** - Re-center all EVE clients
- **Daemon status** - Green = running, Red = stopped
- **Client list** - Shows all EVE clients with active indicator (>)
- **Middle-click drag** - Move the overlay anywhere

## Configuration

Config file: `~/.config/nicotine/config.toml`

Auto-generated on first run based on your display. Example:

```toml
display_width = 1920
display_height = 1080
panel_height = 0        # Set this if you have a taskbar/panel
eve_width = 1037        # ~54% of display width
eve_height = 1080
overlay_x = 10.0
overlay_y = 10.0
```

Edit and restart to apply changes.

## Autostart on Login (Optional)

Create systemd service:

```bash
mkdir -p ~/.config/systemd/user
cat > ~/.config/systemd/user/nicotine.service << 'EOF'
[Unit]
Description=Nicotine - EVE Online Multiboxing
After=graphical-session.target

[Service]
Type=simple
ExecStart=%h/.local/bin/nicotine start
Restart=on-failure

[Install]
WantedBy=default.target
EOF

systemctl --user enable --now nicotine
```

Control with:
- `systemctl --user start nicotine` - Start now
- `systemctl --user stop nicotine` - Stop
- `systemctl --user status nicotine` - Check status

## Architecture

- **Daemon mode**: Maintains X11 connection and state in memory for instant cycling
- **Unix socket IPC**: ~2ms command latency (vs ~50-100ms process spawning)
- **Fire-and-forget X11**: Non-blocking window activation
- **Cached atoms**: Pre-cached `_NET_ACTIVE_WINDOW` for zero overhead

## Requirements

- **X11** (not Wayland)
- **wmctrl** - Window management
- **xbindkeys** - Mouse button bindings (optional, for hotkeys)

### Install Dependencies

**Arch Linux:**
```bash
sudo pacman -S wmctrl xbindkeys
```

**Ubuntu/Debian:**
```bash
sudo apt install wmctrl xbindkeys
```

**Fedora:**
```bash
sudo dnf install wmctrl xbindkeys
```

## Performance Notes

This tool is optimized for EVE Online multiboxing where instant client switching is critical during combat. The daemon architecture ensures cycling never blocks on EVE client rendering, even when opening resource-intensive UI elements like the New Eden Store.

## Building from Source

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Build
cargo build --release

# Binary at: target/release/nicotine
```

## License

MIT

## Credits

Inspired by EVE-O Preview for Windows.
