---
title: Introduction to DBus
description: Small introduction to D-Bus principles and demo with different tools and libraries.
date: 2022-03-23T15:39:00+02:00
# tldr:
draft: true
tags: [dbus, ipc]
---

Hi everybody!

I decided to write this article because during interview for new job opportunities, I got asked several times if I knew
what is D-Bus about and if I ever worked with this technology. Being more embedded-oriented, I *obsiously* answered "no"
in shame and tried to keep my face on until the end of the meeting...

Anyway, let's start with the article!


## What is D-Bus?

Created in 2002, D-Bus is part of the [*freedesktop.org*](https://freedesktop.org) project and offers an efficient and
easy-to-use *Inter-Process Communication* (IPC) and *Remote Procedure Calling* (RPC) mechanism. It aims at replacing the
plethora of IPC frameworks already available (shared memory, memory-mapped file, pipe, socket, ...) with a simpler,
single, and secure protocol.

{{< figure src="/images/2022-03-23-dbus_introduction/01-without_dbus.svg" caption="Without D-Bus" >}}

{{< figure src="/images/2022-03-23-dbus_introduction/02-with_dbus.svg" caption="With D-Bus" >}}

As the name implies, D-Bus uses a logical and software *bus* over which connected applications may communicate. Under
the hood, D-Bus uses the socket mechanism to allow communication. This communication takes place via a simple object
model. Clients connected to the bus may query for the availability of objects, call remote methods on them, and request
notifications for the various signals these objects emit.


## How does D-Bus work?

D-Bus is composed of several components:

- `libdbus`: A low-level library.
- `dbus-daemon`: A daemon based on `libdbus`. Handles and controls data transfers between D-Bus publishers and
    subscribers.
- Two buses:
    1. `system` bus: Single bus that is shared across all users. It is dedicated to system services and low-level events
        such as network connection, USB devices, ...
    2. `session` bus: One instance per user session. Provides desktop services to user applications.
- A security mechanism using `policy` files.


## Key principles

D-Bus is working with different elements:

1. Services
2. Objects
3. Interfaces
4. Clients -- applications using a D-Bus service

A D-Bus service contains object(s), which implement(s) interface(s). Clients may query these objects and their
interfaces.

{{< figure src="/images/2022-03-23-dbus_introduction/03-dbus_structure.svg" caption="D-Bus service overview" class="center" >}}

### Services

An application can expose its *services* to all D-Bus users by registering to a bus instance (`system` or `session`).
Each of these services are a collection of *objects*, providing a specific set of features. On connection, applications
are automatically assigned a unique and effectively random bus name (*e.g.* `:1.40`).

The bus name mechanism is similar to dynamic IP allocation paired with DNS name resolution. Freshly connected
applications are assigned a random bus name. In order to prevent clients


# References

1. [DBus Overview](https://pythonhosted.org/txdbus/dbus_overview.html)
2. [Understanding D-Bus](https://bootlin.com/pub/conferences/2016/meetup/dbus/josserand-dbus-meetup.pdf)
