# screen-mirror

Cursor-following projector mirror for GNOME Wayland. Renders the
monitor your cursor is currently on (with magnifier lens, key
overlay, focused-window chip, clock, and source-switch banner) onto
your HDMI projector.

## What it does

- **Follow the cursor** — whichever monitor your mouse is on is
  mirrored to the projector. Cross to another monitor and the
  projector switches.
- **Pan + zoom** — wider source monitors (e.g. ultrawide) are zoomed
  in and pan with the cursor so projected content stays readable.
- **Magnifier lens** — a circular high-zoom region around the cursor;
  auto-hides on idle.
- **Cursor barrier** — the cursor is warped back if it crosses onto
  the projector itself, so you can't click your way out of the
  mirror's source.
- **Key overlay** — every typed key floats above the cursor (words
  coalesce, fade as a chunk, Caps Lock indicator, smart backspace).
- **Focused-app chip + clock** — top-left shows the app icon, name
  and window title of whatever window has focus; top-right shows the
  time.
- **Fullscreen detect** — when the source app goes fullscreen
  (YouTube, video, etc.) the projector switches to aspect-fit with a
  black/gray backdrop and clears all chrome except the magnifier.

## Dependencies

Runs on GNOME Wayland with the two patched components below. Without
them the script still runs but the cursor and keyboard features
won't work:

| Component | Fork |
|---|---|
| Patched gnome-shell (enables `Shell.Eval`, disables extension version checks, auto-grants Access portal) | [kierandrewett/gnome-shell `jailbreak`](https://github.com/kierandrewett/gnome-shell/tree/jailbreak) |
| Patched Mutter (broadcasts every Wayland key event on the session bus so the overlay can read keys without `/dev/input` access) | [kierandrewett/mutter `jailbreak`](https://github.com/kierandrewett/mutter/tree/jailbreak) |

The shell-side cursor / fullscreen / focus polling is installed via
`Shell.Eval` at startup — that requires the gnome-shell patch.

## Install

Drop the script anywhere and run with Python 3 + GTK 4 + GStreamer +
PyGObject (Fedora packages it all out of the box).

The provided `screen-mirror.service` unit autostarts on login and
respawns on crash:

```sh
mkdir -p ~/.config/systemd/user
cp screen-mirror.service ~/.config/systemd/user/
systemctl --user enable --now screen-mirror.service
```

To suppress Mutter direct-scanout (the YouTube-fullscreen freeze
workaround) drop:

```
MUTTER_DEBUG_DISABLE_DIRECT_SCANOUT=1
```

into `~/.config/environment.d/95-screen-mirror.conf` and log out / in.

## Verify

`./verify-jailbreak` sanity-checks that the patched gnome-shell is
running.
