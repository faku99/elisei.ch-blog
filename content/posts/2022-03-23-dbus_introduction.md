---
title: Introduction to DBus
description: Small introduction to D-Bus principles and demo with different tools and libraries.
date: 2022-03-23T15:39:00+02:00
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

{{<flex-center>}}
    {{<figure src="/images/2022-03-23-dbus_introduction/01-without_dbus.svg" caption="Without D-Bus">}}
    {{<figure src="/images/2022-03-23-dbus_introduction/02-with_dbus.svg" caption="With D-Bus">}}
{{</flex-center>}}

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

{{<figure src="/images/2022-03-23-dbus_introduction/03-dbus_structure.svg" caption="D-Bus service overview" class="center">}}

### Services

An application can expose its *services* to all D-Bus users by registering to a bus instance (`system` or `session`).
Each of these services are a collection of *objects*, providing a specific set of features. On connection, applications
are automatically assigned a unique and effectively random bus name (*e.g.* `:1.40`).

The bus name mechanism is similar to dynamic IP allocation paired with DNS name resolution. Freshly connected
applications are assigned a random bus name. In order to prevent clients from needing to discover -- in some dark
ways -- this random bus name, the service may instead request a more *human-readable* name: the **well-known name**
(*e.g.* `com.example`).

### Objects

Objects are attached to one service and implement one or several interfaces. They can be dynamically created or removed.
D-Bus objects are identified within a service via their **object path**, which looks just like a standard Unix path
(*e.g.* `/`, `/object`).

### Interfaces

Interfaces define the methods and signals supported by D-Bus objects. They also have a unique name, similar to Java
interface names, using dots (*e.g.* `com.example.Interface`). A D-Bus interface contains several members: properties,
methods, and signals.

D-Bus exposes a few standard interfaces, such as:

* `org.freedesktop.DBus.Introspectable`: Provides an introspection mechanism which allow to expose information about the
    object (*e.g.* interfaces, methods, and signals).
* `org.freedesktop.DBus.Peer`: Provides methods to know if a connection is alive.
* `org.freedesktop.DBus.Properties`: Provides methods and signals to handle properties.
* `org.freedesktop.DBus.ObjectManager`: Provides an helpful API to handle sub-tree objects.

#### Properties

As you would expect, D-Bus properties are directly read/write accessible fields. As said before, those properties are
provided through the `org.freedesktop.DBus.Properties` standard interface. They can be of different types, which
are defined by the D-Bus specification (and represented by characters):

| Character  | Code Data Type                             |
|------------|--------------------------------------------|
| y          | 8-bit unsigned integer                     |
| b          | boolean value                              |
| n          | 16-bit signed integer                      |
| q          | 16-bit unsigned integer                    |
| i          | 32-bit signed integer                      |
| u          | 32-bit unsigned integer                    |
| x          | 64-bit signed integer                      |
| t          | 64-bit unsigned integer                    |
| d          | double-precision floating point (IEEE 754) |
| s          | UTF-8 string (no embedded nul characters)  |
| o          | D-Bus Object Path string                   |
| g          | D-Bus Signature string                     |
| a          | Array                                      |
| (          | Structure start                            |
| )          | Structure end                              |
| v          | Variant type (described below)             |
| {          | Dictionary/Map begin                       |
| }          | Dictionary/Map end                         |
| h          | Unix file descriptor                       |

#### Methods

D-Bus methods may accept any number of arguments and may return any number of values, including none. They look like any
method you would know from other languages.

`org.freedesktop.DBus.Properties`:
- `Get (String interface_name, String property_name) => Variant value`
- `GetAll (String interface_name) => Dict of {String, Variant} properties`
- `Set (String interface_name, String property_name, Variant value)`

#### Signals

Signals provide a one-to-many, publisher-subscriber mechanism. They are unidirectional and are only sent to clients who
are listening to them. Just like methods, they can contain parameters and return values. However, unlike methods,
signals are entirely asynchronous and may be emitted by D-Bus objects at any time.


## Less theory, more practice



# References

1. [DBus Overview](https://pythonhosted.org/txdbus/dbus_overview.html)
2. [Understanding D-Bus](https://bootlin.com/pub/conferences/2016/meetup/dbus/josserand-dbus-meetup.pdf)
