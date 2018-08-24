Sample init scripts and service configuration for starshipshad
==========================================================

Sample scripts and configuration files for systemd, Upstart and OpenRC
can be found in the contrib/init folder.

    contrib/init/starshipshad.service:    systemd service unit configuration
    contrib/init/starshipshad.openrc:     OpenRC compatible SysV style init script
    contrib/init/starshipshad.openrcconf: OpenRC conf.d file
    contrib/init/starshipshad.conf:       Upstart service configuration file
    contrib/init/starshipshad.init:       CentOS compatible SysV style init script

1. Service User
---------------------------------

All three Linux startup configurations assume the existence of a "starshipsha" user
and group.  They must be created before attempting to use these scripts.
The OS X configuration assumes starshipshad will be set up for the current user.

2. Configuration
---------------------------------

At a bare minimum, starshipshad requires that the rpcpassword setting be set
when running as a daemon.  If the configuration file does not exist or this
setting is not set, starshipshad will shutdown promptly after startup.

This password does not have to be remembered or typed as it is mostly used
as a fixed token that starshipshad and client programs read from the configuration
file, however it is recommended that a strong and secure password be used
as this password is security critical to securing the wallet should the
wallet be enabled.

If starshipshad is run with the "-server" flag (set by default), and no rpcpassword is set,
it will use a special cookie file for authentication. The cookie is generated with random
content when the daemon starts, and deleted when it exits. Read access to this file
controls who can access it through RPC.

By default the cookie is stored in the data directory, but it's location can be overridden
with the option '-rpccookiefile'.

This allows for running starshipshad without having to do any manual configuration.

`conf`, `pid`, and `wallet` accept relative paths which are interpreted as
relative to the data directory. `wallet` *only* supports relative paths.

For an example configuration file that describes the configuration settings,
see `contrib/debian/examples/starshipsha.conf`.

3. Paths
---------------------------------

3a) Linux

All three configurations assume several paths that might need to be adjusted.

Binary:              `/usr/bin/starshipshad`  
Configuration file:  `/etc/starshipsha/starshipsha.conf`  
Data directory:      `/var/lib/starshipshad`  
PID file:            `/var/run/starshipshad/starshipshad.pid` (OpenRC and Upstart) or `/var/lib/starshipshad/starshipshad.pid` (systemd)  
Lock file:           `/var/lock/subsys/starshipshad` (CentOS)  

The configuration file, PID directory (if applicable) and data directory
should all be owned by the starshipsha user and group.  It is advised for security
reasons to make the configuration file and data directory only readable by the
starshipsha user and group.  Access to starshipsha-cli and other starshipshad rpc clients
can then be controlled by group membership.

3b) Mac OS X

Binary:              `/usr/local/bin/starshipshad`  
Configuration file:  `~/Library/Application Support/Starshipsha/starshipsha.conf`  
Data directory:      `~/Library/Application Support/Starshipsha`
Lock file:           `~/Library/Application Support/Starshipsha/.lock`

4. Installing Service Configuration
-----------------------------------

4a) systemd

Installing this .service file consists of just copying it to
/usr/lib/systemd/system directory, followed by the command
`systemctl daemon-reload` in order to update running systemd configuration.

To test, run `systemctl start starshipshad` and to enable for system startup run
`systemctl enable starshipshad`

4b) OpenRC

Rename starshipshad.openrc to starshipshad and drop it in /etc/init.d.  Double
check ownership and permissions and make it executable.  Test it with
`/etc/init.d/starshipshad start` and configure it to run on startup with
`rc-update add starshipshad`

4c) Upstart (for Debian/Ubuntu based distributions)

Drop starshipshad.conf in /etc/init.  Test by running `service starshipshad start`
it will automatically start on reboot.

NOTE: This script is incompatible with CentOS 5 and Amazon Linux 2014 as they
use old versions of Upstart and do not supply the start-stop-daemon utility.

4d) CentOS

Copy starshipshad.init to /etc/init.d/starshipshad. Test by running `service starshipshad start`.

Using this script, you can adjust the path and flags to the starshipshad program by
setting the BITCOIND and FLAGS environment variables in the file
/etc/sysconfig/starshipshad. You can also use the DAEMONOPTS environment variable here.

4e) Mac OS X

Copy org.starshipsha.starshipshad.plist into ~/Library/LaunchAgents. Load the launch agent by
running `launchctl load ~/Library/LaunchAgents/org.starshipsha.starshipshad.plist`.

This Launch Agent will cause starshipshad to start whenever the user logs in.

NOTE: This approach is intended for those wanting to run starshipshad as the current user.
You will need to modify org.starshipsha.starshipshad.plist if you intend to use it as a
Launch Daemon with a dedicated starshipsha user.

5. Auto-respawn
-----------------------------------

Auto respawning is currently only configured for Upstart and systemd.
Reasonable defaults have been chosen but YMMV.
