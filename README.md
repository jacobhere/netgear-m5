# netgear-m5

`netgear-m5.sh` is a command line utility for querying and controlling a [Netgear m5 router](https://www.netgear.com/home/mobile-wifi/hotspots/mr5100/).

I am mostly happy with my Netgear m5 mobile router, but the cellular data connection tends to slow down over time. The connection speed recovers if I reboot the router. I made this utility for an easy way for periodically rebooting the router. At first I thought reconnecting the mobile data connection would suffice, so I implemented commands for connecting and disconnecting as well.

This utility is based on a Perl script I found on an [Netgear community discussion thread](https://community.netgear.com/t5/Mobile-Routers-Hotspots-Modems/MR1100-fails-to-reestablish-the-connection-after-the-hang-up/m-p/1918046). Use at your own risk. I have no affiliation with Netgear.

## Requirements

The utility only requires very common UNIX tools:

- [bash](https://www.gnu.org/software/bash/)
- [curl](https://curl.haxx.se/)
- [mktemp](https://www.gnu.org/software/autogen/mktemp.html)

## Usage

```
Usage:
  netgear-m5.sh status [--json]
  netgear-m5.sh reboot
  netgear-m5.sh connect
  netgear-m5.sh disconnect
  netgear-m5.sh reconnect
  netgear-m5.sh wifi_on
  netgear-m5.sh wifi_off
  netgear-m5.sh -h | --help

Options:
  -h --help  Show usage screen.
  --json     Output full router status in JSON format.

Commands:
  status     Output router status. Default is brief human readable output.
  reboot     Reboot router.
  connect    Turn cellular data connection on.
  disconnect Turn cellular data connection off.
  reconnect  Turn cellular data connection off and on again.
  wifi_on    Turn Wifi ON.
  wifi_off   Turn Wifi OFF.

By default the utility connects router at IP address 10.24.4.1.
Another IP address can be provided environment variable NETGEAR_m5_IP.
```

## Commands

The utility has the following commands for controlling the router:

- status
- reboot
- disconnect
- connect
- reconnect
- wifi_on
- wifi_off

All commands except status require admin password of the router. The utility will ask for the password. The utility does not save the password, but it stores a session cookie received from the router to a temporary file, which is deleted when the utility exists.

If you want to run the utility with no user interaction, you can pipe the password to the utility like this:

```
$ echo $PASSWORD | ./netgear-m5.sh reboot
```

If your router is not at IP address `10.24.4.1`, please provide alternative IP address in environment variable `NETGEAR_m5_IP`. For example, like this:

```
$ NETGEAR_m5_IP=10.0.0.1 ./netgear-m5.sh reboot
```

### status

This command returns basic information about the status of the router. Status information can be queried without the admin password.

```
$ ./netgear-m5.sh status
             Device name: Nighthawk m5
    Battery charge level: 84
              IP address: 192.168.1.99
      Current radio band: LTE B20
        Data transferred: 142027842799
Router connection status: Connected
```

The `--json` flag can be used to store all status information provided by the router.

```
$ ./netgear-m5.sh status --json > model.json
```

### ping

This command sends pings to the network adapter of the router until it responds. After that it waits for the router service API to be available.

```
$ ./netgear-m5.sh ping
Waiting for router startup
Waiting for router services
Router is up
```

### reboot

This command reboots the router

```
$ ./netgear-m5.sh reboot
Password:
Logged in to Nighthawk m5
Rebooting router
Waiting for router shutdown..
Router is down
Waiting for router network adapter..............
Waiting for router services
Router is up
```

### disconnect

This command disconnects the cellular data connection of the router.

```
$ ./netgear-m5.sh disconnect
Password:
Logged in to Nighthawk m5
Disconnected cellular data
```

![mobile data connection disconnected](https://raw.githubusercontent.com/jacobhere/netgear-m5/main/docs/disconnected.png)

### connect

This command connects the cellular data connection of the router.

```
$ ./netgear-m5.sh connect
Password:
Logged in to Nighthawk m5
Connected cellular data
```

![mobile data connection connected](https://raw.githubusercontent.com/jacobhere/netgear-m5/main/docs/connected.png)

### reconnect

This command disconnects and connects the cellular data connection of the router.

```
$ ./netgear-m5.sh reconnect
Password:
Logged in to Nighthawk m5
Disconnected cellular data
Connected cellular data
```
