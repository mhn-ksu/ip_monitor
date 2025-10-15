# ip_monitor
This launch agent controls the periodic running of a bash script that checks
the current IP address against a saved value. If the current IP address has
changed, then an email is created and saved as a draft. This draft can be
viewed on other computers with access to the email account, and the information
used to adjust remote connections.

A Launch Agent is used instead of a cron on MacOS because:
- It works reliable across sleep/wake cycles
- It's the Apple-recommended method
- It runs in your user context (so Mail.app and notification work properly)
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

If you still get errors after `bootstrap`, run:

    launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.user.ipmonitor.plist 2>&1

This will show the actual error message, which will help troubleshoot the
issue.


