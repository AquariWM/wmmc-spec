<!-- This Source Code Form is subject to the terms of the Mozilla Public
   - License, v. 2.0. If a copy of the MPL was not distributed with this
   - file, You can obtain one at https://mozilla.org/MPL/2.0/. -->

# Window Manager MetaClients
This specification describes a collection of X private protocols that allow multiple X clients to
communicate and coordinate their operation with a compliant window manager to independently operate
different parts of that window manager. In simple terms: MetaClients are the 'modules' of a modular
window manager, and this specification allows their communication.

## Protocols
The following protocols are defined by the Window Manager MetaClients specification. So-called 
MetaClients MAY implement one or more of these protocols, however it is RECOMMENDED that no more
than one protocol is implemented by one MetaClient.

Window managers MUST be prepared to accept any MetaClient to communicate with any of the protocols
that the window manager supports and that are listed in the MetaClient's `WM_PROTOCOLS` property.
Window managers MAY NOT choose to accept communication over protocols that were not present in the
MetaClient's `WM_PROTOCOLS` property when the window manager first read that property. MetaClients
MUST provide all WMMC protocols that they SHALL communicate over in their `WM_PROTOCOLS` property.
MetaClients MUST fulfill the requirements of all WMMC protocols which they provide in the
`WM_PROTOCOLS` property.

### `_WMMC_LAYOUT`
The `_WMMC_LAYOUT` protocol allows for the communication between a separate 'layout manager' for
tiling window managers, and the window manager itself.

### `_WMMC_DECORATE`
The `_WMMC_DECORATE` protocol allows for the communication between a separate 'window decorator'
client, and the window manager itself. This separates the visual style of windows from the
management of these windows; this means the same appearance can be kept even after changing window
manager, and the appearance of one's desktop can be changed without having to switch window
manager.

<!-- TODO: The `_WMMC_DECORATE_REQUEST` and `_WMMC_DECORATE_NOTIFY` atoms should be combined, as
   -       there is no difference in their structure. A request can be identified because it will
   -       be sent by the window manager to a `_WMMC_DECORATE` client, and a reply can be
   -       identified because it will be sent by a `_WMMC_DECORATE` client to the root window. -->

#### `_WMMC_DECORATE_REQUEST`
When a window is due for decoration, a window manager participating in the `_WMMC_DECORATE`
protocol will send a `_WMMC_DECORATE_REQUEST` event to the client registered to decorate windows.

As there are situations where the outer window decorations must be a fixed size, particularly in
tiling window layouts, a `_WMMC_DECOREATE` client MUST NOT size its window decoration frame
according to the decorated window's geometry at the time of the `_WMMC_DECORATE_REQUEST`. Because
of this, the request will contain, in addition to the `window` that must be decorated, three other
fields: the `width`, the `height`, and a boolean state.

That boolean state specifies whether the `width` and `height` provided SHALL be treated as the
exact size of the window being decorated, or whether they should be treated as the exact size of
the window decorations.

If the boolean state is the exact size of the window being decorated, this means that the window
decoration frame MUST be _at least_ the specified `width` and `height`, but MAY be larger. In this
case, the client MUST send the `x`, `y`, `width` and `height` of its frame. The `x` and `y`
coordinates MUST be negative or zero, and they describe the offset of the window decorations frame
relative to the top-left corner of the decorated window. Though they will be negative numbers,
they are represented as unsigned integers; because they MUST be negative, there is no need to
allow these integers to be signed: they can only ever be one sign.

If it is the exact size of the window decorations, this means that the window decoration frame
MUST be the specified `width` and height`. In this case, the client MUST send the `x`, `y`, `width`
and `height` that it allocates to the decorated window within this frame in the
`_WMMC_DECORATE_NOTIFY` event that it sends the window manager.

#### `_WMMC_DECORATE_NOTIFY`
Sent by the `_WMMC_DECORATE` client as soon as it knows the position and dimensions of either its
frame or the decorated window within this frame, depending on which boolean state was associated
with this decoration. The client SHALL NOT wait for its window decorations to render before sending
this reply, though it MUST have created its frame window before sending this reply, as this reply
includes the frame created by the `_WMMC_DECORATE` client so that the window manager can reparent
the decorated window to this frame. The window manager MAY do the reparenting immediately, or it
MAY reparent the decorated window at a later time, or even not at all. The `_WMMC_DECORATE` client
MUST NOT expect the window manager to reparent the decorated window to the frame, NOR SHALL it
expect the window manager to reparent the decorated window to the frame at any specific time: it is
up to the window manager as to when and if it will do so. It is, however, RECOMMENDED that the
window manager will reparent the decorated window to the frame when possible.

### `_WMMC_CONFIG`
The `_WMMC_CONFIG` protocol allows for the universal configuration of compliant window managers. It
separates the mode of configuration (for example, a config file or shell commands) from the window
manager and allows the user to choose the type of configuration which they prefer.

### `_WMMC_INPUT`
The `_WMMC_INPUT` protocol allows for the separation between the mode of input, and specific input
bindings, from the functionality of the window manager. The 'keybindings', as well as other types
of input, can be configured and new modes of input can be introduced without requiring changes to
the window manager.
