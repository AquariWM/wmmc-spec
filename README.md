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
The `_WMMC_DECORATE` protocol allows the decoration of windows (i.e. titlebars, borders, etc.)
independent of the window manager, meaning a consistent visual style can be maintained across
window managers.

In the `_WMMC_DECORATE` protocol, window decorations can be considered to be aligned either inside
or outside the space allocated for the window by the window manager. This is particularly useful
when comparing floating and tiling layouts: in a floating window layout, it might make sense that
window decorations are always 'added' to a window; they extend the area occupied by the window, so
that they are not shrinking the window being decorated, however it might make more sense in a
tiling window layout, where space is 'strictly' allocated to windows, that a window and its
decorations never take up more or less room than is allocated by the window manager.

A window manager participating in the `_WMMC_DECORATE` protocol SHALL send a `_WMMC_DECORATE` event
to exactly one `_WMMC_DECORATE` client. This event SHALL contain the identifier for the window that
shall be decorated, the new width and height allocated by the layout to this window (or, if not,
the current width and height of the window), and a boolean value representing the alignment of
window decorations, with `0`/`false` representing that decorations SHALL be placed outside the
decorated window, and `1`/`true` representing that decorations SHALL be placed inside the decorated
window. The window manager SHALL NOT include any `x` or `y` position with this event; these fields
MUST be set to `0`, and are only for `_WMMC_DECORATE` clients to send events to the window manager.

If decorations are aligned outside the allocated space, the size of the window is fixed and the
frame created by the decorator (i.e. the `_WMMC_DECORATE` client) MUST be _at least_ the same width
and height, plus any `x` or `y` offset of the frame, as the decorated window. In this case, the
decorator MUST send a `_WMMC_DECORATE` event back to the window manager indicating the position 
(`x` and `y` unsigned offsets from the top-left of the decorated window; ie. `window_x - frame_x`
and `window_y - frame_y`) and size (width and height) of the created frame. The `_WMMC_DECORATE`
event sent to the window manager MUST also include the window ID of the created frame.

If decorations are aligned inside the allocated space, the size of the window decorations frame is
fixed and MUST be created by the decorator according to the exact width and height provided by the
window manager. In this case, the decorator MUST send a `_WMMC_DECORATE` event back to the window
manager indicating the position (relative to the frame's top-left corner) and size to apply to the
decorated window when placing it within the frame. The `_WMMC_DECORATE` event sent to the window
manager MUST also include the window ID of the created frame.

### `_WMMC_CONFIG`
The `_WMMC_CONFIG` protocol allows for the universal configuration of compliant window managers. It
separates the mode of configuration (for example, a config file or shell commands) from the window
manager and allows the user to choose the type of configuration which they prefer.

### `_WMMC_INPUT`
The `_WMMC_INPUT` protocol allows for the separation between the mode of input, and specific input
bindings, from the functionality of the window manager. The 'keybindings', as well as other types
of input, can be configured and new modes of input can be introduced without requiring changes to
the window manager.
