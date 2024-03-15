# Outline CLI

[![Version](https://img.shields.io/github/v/release/Kir-Antipov/outline-cli?sort=date&style=flat&label=version&cacheSeconds=3600)](https://github.com/Kir-Antipov/outline-cli/releases/latest)
[![License](https://img.shields.io/github/license/Kir-Antipov/outline-cli?style=flat&cacheSeconds=36000)](https://github.com/Kir-Antipov/outline-cli/blob/HEAD/LICENSE.md)

[Outline](https://getoutline.org/), developed by [Jigsaw](https://jigsaw.google.com/), is a great project aimed at simplifying the task of creating self-hosted VPN servers down to a matter of seconds. However, while the server deployment with Outline is commendable, its client-side experience falls short. Currently, the primary method of connecting to an [Outline server](https://github.com/Jigsaw-Code/outline-server/) is via the [Outline Client](https://github.com/Jigsaw-Code/outline-apps/) - an Electron *(or Cordova for mobile devices)* app - which unfortunately presents lots of issues across different platforms. It is somewhat buggy on Windows, notably unreliable on macOS, and entirely non-functional on Linux *(at least if Linux means more than just Ubuntu to you)*.

Fortunately, at least for Linux users, there's an alternative - a [CLI solution](https://github.com/Jigsaw-Code/outline-sdk/tree/main/x/examples/outline-cli/) hidden within the "experimental" section of the [outline-sdk](https://github.com/Jigsaw-Code/outline-sdk/) repo, which only supports Linux at the moment of writing. Despite its minimalistic design, the CLI performs its sole function - establishing a connection to a selected server and rerouting all traffic through it - quite efficiently and reliably. Essentially, it embodies the Unix philosophy in software development. However, it may not be very user-friendly to manually launch/restart it, manage your access keys, and forcibly stop it when you wish to disconnect from the VPN server, and so on. This is where this project comes in.

This project, essentially a wrapper over the official [outline-cli](https://github.com/Jigsaw-Code/outline-sdk/tree/main/x/examples/outline-cli/) provided by [Jigsaw](https://github.com/Jigsaw-Code/), aims to make its usage easier. Comprised of just a few shell scripts, it enhances the default experience into a near-perfect one - unachievable by a buggy Electron app ;)

----

## License

Licensed under the terms of the [MIT License](https://github.com/Kir-Antipov/outline-cli/blob/HEAD/LICENSE.md).
