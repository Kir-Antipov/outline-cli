# Outline CLI

[![Version](https://img.shields.io/github/v/release/Kir-Antipov/outline-cli?sort=date&style=flat&label=version&cacheSeconds=3600)](https://github.com/Kir-Antipov/outline-cli/releases/latest)
[![License](https://img.shields.io/github/license/Kir-Antipov/outline-cli?style=flat&cacheSeconds=36000)](https://github.com/Kir-Antipov/outline-cli/blob/HEAD/LICENSE.md)

<img alt="Outline CLI Icon" src="media/icon.png" width="128">

[Outline](https://getoutline.org/), developed by [Jigsaw](https://jigsaw.google.com/), is a great project aimed at simplifying the task of creating self-hosted VPN servers down to a matter of seconds. However, while the server deployment with Outline is commendable, its client-side experience falls short. Currently, the primary method of connecting to an [Outline server](https://github.com/Jigsaw-Code/outline-server/) is via the [Outline Client](https://github.com/Jigsaw-Code/outline-apps/) - an Electron *(or Cordova for mobile devices)* app - which unfortunately presents lots of issues across different platforms. It is somewhat buggy on Windows, notably unreliable on macOS, and entirely non-functional on Linux *(at least if Linux means more than just Ubuntu to you)*.

Fortunately, at least for Linux users, there's an alternative - a [CLI solution](https://github.com/Jigsaw-Code/outline-sdk/tree/main/x/examples/outline-cli/) hidden within the "experimental" section of the [outline-sdk](https://github.com/Jigsaw-Code/outline-sdk/) repo, which only supports Linux at the moment of writing. Despite its minimalistic design, the CLI performs its sole function - establishing a connection to a selected server and rerouting all traffic through it - quite efficiently and reliably. Essentially, it embodies the Unix philosophy in software development. However, it may not be very user-friendly to manually launch/restart it, manage your access keys, and forcibly stop it when you wish to disconnect from the VPN server, and so on. This is where this project comes in.

This project, essentially a wrapper over the official [outline-cli](https://github.com/Jigsaw-Code/outline-sdk/tree/main/x/examples/outline-cli/) provided by [Jigsaw](https://github.com/Jigsaw-Code/), aims to make its usage easier. Comprised of just a few shell scripts, it enhances the default experience into a near-perfect one - unachievable by a buggy Electron app ;)

----

## Features

Obviously, the official CLI provides you with the ability to connect to servers deployed by the Outline Manager, or to any Shadowsocks server, for that matter. Here is what this project offers on top of that:

- Access key management
- Option to specify a custom DNS resolver instead of the hardcoded one
- Ability to connect to devices on your local network while the VPN is active
- Automatic reconnection to the VPN server upon reboot/network status change *(requires `NetworkManager`)*
- Hotkeys to toggle the current connection status, quickly connect to a specific server, suspend the current connection, and more *(requires [`keyd`](https://github.com/rvaiya/keyd/))*

----

## Installation

The installation process is quite straightforward: just clone this repo and run the `install` script.

```sh
git clone https://github.com/Kir-Antipov/outline-cli
cd outline-cli
sudo ./install -y
```

This will:

1) Build and install the official [`outline-cli`](https://github.com/Jigsaw-Code/outline-sdk/tree/main/x/examples/outline-cli/)
   1) Clone [`Jigsaw-Code/outline-sdk`](https://github.com/Jigsaw-Code/outline-sdk/)
   2) Apply a patch that allows you to specify a custom DNS resolver instead of the hardcoded one
   3) Download the latest version of Go (locally, not globally)
   4) Build the app
   5) Copy the resulting binary to `/usr/local/sbin/__vpn_connect`
   6) Clean up
2) Copy the `__vpn_manager` script to `/usr/local/sbin/__vpn_manager`
3) Create a symlink for `__vpn_manager`, enabling you to call it using the shorthand `vpn`
4) Copy the `vpn-manager-refresh` script to `/etc/NetworkManager/dispatcher.d/vpn-manager-refresh`, ensuring it executes each time your network connection status changes
5) Unbork the LAN by creating two new routing rules that prevent local traffic from being redirected to your VPN server
6) If you have [`keyd`](https://github.com/rvaiya/keyd/) installed, copy a `keyd` configuration to `/etc/keyd/vpn-manager.conf`

If you wish to configure the process described above, you can run the installation script without the `-y` flag, allowing you to guide it and adjust the results according to your preferences.

----

## Usage

```
Usage: vpn <command> [<args>] [<options>]

Manage Shadowsocks server connections and related access keys.

Examples:
  sudo vpn add "ss://..." "Geneva"
  sudo vpn connect geneva
  sudo vpn disconnect

Commands:
  add <key> [<name>]        Add a new access key
  remove <name | index>     Remove the designated access key
  list [-f <format>]        List all stored access keys
  connect [<name | index>]  Connect to a server
  disconnect [-s]           Disconnect from the current server
  toggle                    Toggle the current connection
  status                    Return the current connection status

Options:
  -h, --help                Display this help text and exit
  -q, --quiet               Suppress all normal output
  -n, --notify              Display a notification
  -s, --suspend             Suspend the current connection;
                            It will be re-established later
  -f, --format <format>     Print a key according to the <format>;
                            The formats are: %name%, %ip%, %index%
```

First and foremost, akin to the GUI app, you must add an access key. For example:

```sh
sudo vpn add "ss://YFhvLUKmUBJwEsSfL65ShwAOzuLDNN0HTBh9rCb2yhIJdrMXBhgP1DXBm4y7@42.42.42.42:52683/?outline=1" "Geneva"
```

You can list all stored access keys using the `sudo vpn list` command:

```
1  Geneva  42.42.42.42:52683
```

Once done, you're all set! Use:

```sh
sudo vpn connect "Geneva"
```

or one of these alternatives:

```sh
# The access key name is case insensitive.
sudo vpn connect geneva

# You can also use the saved access key's ID
# (i.e., its ordinal number from the `list` output).
sudo vpn connect 1

# You can also omit the name/ID entirely.
# You will then connect to the last server you connected to.
sudo vpn connect
```

to connect to the desired VPN server.

Whenever you wish to disconnect from the current server, simply use:

```sh
sudo vpn disconnect
```

And that about covers the most essential functionalities you'll need.

----

## Hotkeys

If you have [`keyd`](https://github.com/rvaiya/keyd/) installed and followed the standard installation script, you now have the following global hotkeys available:

| Hotkey | Description |
|:-------|:------------|
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd>                | Toggle the current connection                           |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>-</kbd> | Disconnect from the current server                      |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>=</kbd> | Connect to a server you were last connected to          |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>0</kbd> | Show the current connection status                      |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>1</kbd> | Connect to a server using the 1<sup>st</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>2</kbd> | Connect to a server using the 2<sup>nd</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>3</kbd> | Connect to a server using the 3<sup>rd</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>4</kbd> | Connect to a server using the 4<sup>th</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>5</kbd> | Connect to a server using the 5<sup>th</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>6</kbd> | Connect to a server using the 6<sup>th</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>7</kbd> | Connect to a server using the 7<sup>th</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>8</kbd> | Connect to a server using the 8<sup>th</sup> access key |
| <kbd>Ctrl</kbd> + <kbd>Super</kbd> + <kbd>v</kbd> + <kbd>9</kbd> | Connect to a server using the 9<sup>th</sup> access key |

----

## Custom DNS Resolver

The Outline Client hardcodes its preferred DNS resolver. This has already caused many problems for numerous users in the past and will undoubtedly cause even more issues in the future.

While striving for the perfect solution is understandable, "perfect" can often be the enemy of "good." This is precisely what we see here, as after all these years, there is still no option to override these hardcoded values. Hence, this project proposes a simple eight-line patch that allows you to supply a DNS resolver of your choice to the Outline Client.

If you followed the standard installation script, you can specify the DNS resolver you prefer directly in your access key via the new `dns` URL parameter, as demonstrated below:

```diff
- ss://.../?outline=1
+ ss://.../?outline=1&dns=1.1.1.1
```

----

## Legal Notice

This project is an independent work and is not affiliated, endorsed, authorized, or sponsored by Jigsaw LLC or any of its subsidiaries or affiliates. Outline and the Outline logo are trademarks and/or registered trademarks of Jigsaw LLC in the U.S. and/or other countries. All rights reserved to their respective owners.

The purpose of this project is purely educational and non-commercial. The code and information found within this project are for educational use only and are not intended for any kind of commercial use.

Use this software at your own risk. The creators cannot be held responsible for any misuse or damages caused by this software.

This notice serves as a disclaimer. Any violations of Jigsaw's policies or trademarks are not intentional.

----

## License

Licensed under the terms of the [MIT License](https://github.com/Kir-Antipov/outline-cli/blob/HEAD/LICENSE.md).
