**HomeScreen GUI Application / HomeScreen Service Guide**
====
<div align="right">Revision: 0.1</div>
<div align="right">TOYOTA MOTOR CORPORATION</div>
<div align="right">Advanced Driver Information Technology</div>
<div align="right">26th/Sep/2017</div>

* * *

<div id="Table\ of\ content"></div>

## Table of content
- [Target reader of this document](#Target\ reader\ of\ this\ document)
- [Overview](#Overview)
- [Getting Start](#Getting\ Start)
	- [Supported environment](#Supported\ environment)
	- [Build](#Build)
	- [Configuring](#Configuring)
	- [How to call HomeScreen APIs from your Application?](#How\ to\ call\ HomeScreen\ APIs\ from\ your\ Application?)
- [Supported usecase](#Supported\ usecase)
- [Software Architecture](#Software\ Architecture)
- [API reference](#API\ reference)
- [Sequence](#Sequence)
	- [Initialize](#InitializeSequence)
	- [Tap Shortcut](#TapShortcutSequence)
	- [On Screen Message / Reply Sequence](#OnScreenMessageSequence)
- [Sample code](#Sample\ code)
- [Limitation](#Limitation)
- [Next Plan](#Next\ Plan)

* * *

<div id="Target\ reader\ of\ this\ document"></div>

## Target reader of this document
Application developer whose software uses HomeScreen.

* * *

<div id="Overview"></div>

## Overview
HomeScreen is built with a GUI application created with Qt(referred as HomeScreenGUI), and a service running on afb-daemon (referred as HomeScreenBinder).
HomeScreen can start/switch applications run in AGL, also displays information such as onscreen messages.

You can find these projects in AGL gerrit.

homescreen-2017(HomeScreenGUI):
    https://gerrit.automotivelinux.org/gerrit/#/admin/projects/staging/homescreen-2017
agl-service-homescreen-2017(HomeScreenBinder's binding library):
    https://gerrit.automotivelinux.org/gerrit/#/admin/projects/apps/agl-service-homescreen-2017
libhomescreen(library for application to communication with HomeScreenBinder):
    https://gerrit.automotivelinux.org/gerrit/#/admin/projects/src/libhomescreen

Also HomeScreenGUI is using libwindowmanager.

<div id="Getting\ Start"></div>

## Getting Start

<div id="Supported\ environment"></div>

### Supported environment

| Item        | Description                       |
|:------------|:----------------------------------|
| AGL version | Electric Eel                      |
| Hardware    | Renesas R-Car Starter Kit Pro(M3) |


<div id="Build"></div>

### Build

**Download recipe**

```
$ mkdir WORK
$ cd WORK
$ repo init -u https://gerrit.automotivelinux.org/gerrit/AGL/AGL-repo
$ repo sync

```

Then you can find the following recipes.

* `meta-agl-devel/meta-hmi-framework/homescreen-2017`

* `meta-agl-devel/meta-hmi-framework/agl-service-homescreen-2017`

* `meta-agl-demo/recipes-demo-hmi/libhomescreen`


**Bitbake**

```
$ source meta-agl/scripts/aglsetup.sh -m m3ulcb agl-demo agl-devel agl-appfw-smack agl-hmi-framework
$ bitbake agl-demo-platform
```


* * *

<div id="Configuring"></div>

### Configuring
To use HomeScreen API, an application shall paste the following configuration definition into "config.xml" of application.

```
<feature name="urn:AGL:widget:required-api">
	<param name="homescreen" value="ws" />
</feature>
```

* * *

<div id="How\ to\ call\ HomeScreen\ APIs\ from\ your\ Application?"></div>

### How to call HomeScreen APIs from your Application?
HomeScreen provides a library which is called "libhomescreen".
This library treats "json format" as API calling.
For example, if an application wants to call "tap_shortcut()" API, the you should implement as below.

At first the application should create the instance of libhomescreen.

```
LibHomeScreen* libhs;
libhs = new LibHomeScreen();
libhs->init(port, token);
```

The port and token is provided by Application Framework

Execute the "tapShortcut()" function.

```
libhs->tapShortcut("application_name");
```

Regarding the detail of tap_shortcut() API, please refer [this](#HomeScreen\ API) section.
The first parameter is the name of API, so in this case "tap_shortcut" is proper string.
And the second parameter corresponds to arguments of "connect()" API.


See also our [Sample code](#Sample\ code).


<br />

* * *

<div id="Supported\ usecase"></div>

## Supported usecase
1. HomeScreenGUI sending ShortCut Icon tapped event to applications
	- Applications using libhomescreen to subscribe the tapShortcut event,
        HomeScreenGUI will send ShortCut Icon tapped event to applications.
2. Display OnScreen messages
	- Applications sending OnScreen messages to homescreen-service, and OnScreenAPP
        will get these message and display.
3. Get OnSreen Reply event
	- When OnScreen messages is displaying, OnScreenAPP will send a reply event to applications.

* * *

<div id="Software\ Architecture"></div>

## Software Architecture
The architecture of HomeScreen is shown below.
HomeScreen is the service designed to be used by multiple applications.
Therefore HomeScreen framework consists on two binder layers. Please refer the following figure.
The upper binder is for application side security context for applications. The lower binder is for servide side security context.
Usually application side binder has some business logic for each application, so the number of binders depend on the number of applications which use HomeScreen.
On the other hand, regarding lower binder there is only one module in the system. This binder receives all messages from multiple applications (in detail, it comes from upper layer binder).

The communication protocols between libhomescreen and upper binder, upper binder and lower binder, lower binder (homescreen-binding) are WebSocket.

![software-stack.png](parts/software-stack.png)

* * *

<div id="API%20reference"></div>

## API reference
"libhomescreen" and "agl-service-homescreen-2017" provides several kinds of APIs.

<div id="Home\ Screen\ Specific\ API"></div>

### HomeScreen Specific API

- [LibHomeScreen ()](api-ref/html/de/dd0/class_lib_home_screen.html#a724bd949c4154fad041f96a15ef0f5dc)
- [init (const int port, const std::string &token)](api-ref/html/de/dd0/class_lib_home_screen.html#a6a57b573cc767725762ba9beab032220)
- [tapShortcut(const char *application_name)](api-ref/html/de/dd0/class_lib_home_screen.html#afb571c9577087b47065eb23e7fdbc903)
- [onScreenMessage(const char *display_message)](api-ref/html/de/dd0/class_lib_home_screen.html#ac336482036a72b51a822725f1929523c)
- [onScreenReply(const char *reply_message)](api-ref/html/de/dd0/class_lib_home_screen.html#a6c065f41f2c5d1f58d2763bfb4da9c37)
- [registerCallback (void(*event_cb)(const std::string &event, struct json_object *event_contents), void(*reply_cb)(struct json_object *reply_contents), void(*hangup_cb)(void)=nullptr)](api-ref/html/de/dd0/class_lib_home_screen.html#a2789e8a5372202cc36f48e71dbb9b7cf)
- [set\_event\_handler(enum EventType et, handler_func f)](api-ref/html/de/dd0/class_lib_home_screen.html#ab1b0e08bf35415de9064afed899e9f85)
- [call (const string& verb, struct json_object* arg)](api-ref/html/de/dd0/class_lib_home_screen.html#a527b49dcfe581be6275d0eb2236ba37f)
- [call (const char* verb, struct json_object* arg)](api-ref/html/de/dd0/class_lib_home_screen.html#ab5e8e8ab7d53e0f114e9e907fcbb7643)
- [subscribe (const string& event_name)](api-ref/html/de/dd0/class_lib_home_screen.html#aa4c189807b75d070f567967f0d690738)
- [unsubscribe (const string& event_name)](api-ref/html/de/dd0/class_lib_home_screen.html#aac03a45cbd453ba69ddb00c1016930a6)

* * *

<div id="Sequence"></div>

## Sequence

<div id="InitializeSequence"></div>

### Initialize Sequence
![initialize-registercallback.svg](parts/initialize-registercallback.svg) * deprecated
![initialize-set-event-handler](parts/initialize-set-event-handler.svg)

<div id="TapShortcutSequence"></div>

### Tap Shortcut Sequence
![tap_shortcut.svg](parts/tap_shortcut.svg)

<div id="OnScreenMessageSequence"></div>

### On Screen Message / Reply Sequence
![on_screen_message.svg](parts/on_screen_message.svg)


<div id="Sample\ code"></div>

# Sample code
You can find sample implementation of HomeScreen as below.

* `libhomescreen/sample/simple-egl`

* `libhomescreen/sample/template`

### Appendix

```
@startuml
title Application initialization phase (ex. registerCallback)
entity App
entity HomeScreenBinder
entity HomeScreenGUI
App->HomeScreenBinder: init(port, token)
App->HomeScreenBinder: subscribe()

note over HomeScreenBinder
    Register the event the App wishes to receive
    ・tap_shortcut
    ・on_screen_message
    ・on_screen_reply
end note

App->HomeScreenBinder: registerCallback()

@enduml
```

```
@startuml
title Application initialization phase (ex. set_event_handler)
entity App
entity HomeScreenBinder
entity HomeScreenGUI
App->HomeScreenBinder: init(port, token)
App->HomeScreenBinder: set_event_handler()

note over HomeScreenBinder
    setup event handler the App wishes to receive
    ・LibHomeScreen::Event_TapShortcut
    ・LibHomeScreen::Event_OnScreenMessage
    ・LibHomeScreen::Event_OnScreenReply
end note

@enduml
```

```
@startuml
title Application Callback Event TapShortcut phase
entity App
entity HomeScreenBinder
entity HomeScreenGUI
App->HomeScreenBinder: set_event_handler()

note over App
    LibHomeScreen::Event_TapShortcut
end note

HomeScreenGUI->HomeScreenBinder: tapShortcut(application_name)
HomeScreenBinder->App: event_handler(application_name)
@enduml
```

```
@startuml
title Application Callback Event On Screen Message / Reply phase
entity App
entity HomeScreenBinder
entity HomeScreenGUI

HomeScreenGUI->HomeScreenBinder: set_event_handler()

note over HomeScreenGUI
    LibHomeScreen::Event_OnScreenMessage
end note


App->HomeScreenBinder: set_event_handler()

note over App
    LibHomeScreen::Event_OnScreenReply
end note

App->HomeScreenBinder: onScreenMessage(display_message)
HomeScreenBinder->HomeScreenGUI: event_handler(display_message)
HomeScreenGUI->HomeScreenBinder: onScreenReply(reply_message)
HomeScreenBinder->App: event_handler(reply_message)
@enduml
```
