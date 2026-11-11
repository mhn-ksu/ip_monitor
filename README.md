# ip_monitor
This launch agent controls the periodic running of a bash script that checks
the current IP address against the value saved in `~/Documents/ip_address.txt`.
If the current IP address has changed, the file is updated with the new address
and a notification is posted. Because `~/Documents` is synced by OneDrive, the
file can be read on other computers, and the information used to adjust remote
connections.

A Launch Agent is used instead of a cron on MacOS because:
- It works reliable across sleep/wake cycles
- It's the Apple-recommended method
- It runs in your user context (so file access and notifications work properly)
- It persists across reboots

# Managing the service

## Copy the plist to `~/Library/LaunchAgents`

    cp ipmonitor/com.user.ipmonitor.plist ~/Library/LaunchAgents/com.user.ipmonitor.plist

## Load the LaunchAgent

    launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.user.ipmonitor.plist

## Start/enable the service
    launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.user.ipmonitor.plist

## Stop/disable the service
    launchctl bootout gui/$(id -u) ~/Library/LaunchAgents/com.user.ipmonitor.plist

## Check if it's running
    launchctl list | grep ipmonitor

## Kick it manually (if already loaded)
    launchctl kickstart gui/$(id -u)/com.user.ipmonitor

## View logs

    tail -f /tmp/ipmonitor.log /tmp/ipmonitor.err

# Troubleshooting
Make sure the plist file path is correct. Verify with:

    ls -la ~/Library/LaunchAgents/com.user.ipmonitor.plist

Verify you updated the user name in the plist.

Check plist syntax

    plutil -lint ~/Library/LaunchAgents/com.user.ipmonitor.plist

Ensure the script is executable:

    chmod +x ip_monitor

If `/tmp/ipmonitor.err` shows `Operation not permitted` when writing the file,
macOS privacy protection is blocking access to `~/Documents`. Grant the bash
binary the script runs under (`/bin/bash`, or Homebrew's bash if that is first
on the PATH) Full Disk Access in System Settings → Privacy & Security.

If you still get errors after `bootstrap`, run:

    launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.user.ipmonitor.plist 2>&1

This will show the actual error message, which will help troubleshoot the
issue.


