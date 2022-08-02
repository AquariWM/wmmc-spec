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

### `_WMMC_CONFIG`
The `_WMMC_CONFIG` protocol allows for the universal configuration of compliant window managers. It
separates the mode of configuration (for example, a config file or shell commands) from the window
manager and allows the user to choose the type of configuration which they prefer.

### `_WMMC_INPUT`
The `_WMMC_INPUT` protocol allows for the separation between the mode of input, and specific input
bindings, from the functionality of the window manager. The 'keybindings', as well as other types
of input, can be configured and new modes of input can be introduced without requiring changes to
the window manager.
