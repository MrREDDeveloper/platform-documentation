---
id: uiperkpurchasebutton
name: UIPerkPurchaseButton
title: UIPerkPurchaseButton
tags:
    - API
---

# UIPerkPurchaseButton

A UIControl for a button which allows players to purchase perks within a game. Similar to `UIButton`, but designed to present a consistent purchasing experience for players across all games. Inherits from [UIControl](uicontrol.md).

## Properties

| Property Name | Return Type | Description | Tags |
| -------- | ----------- | ----------- | ---- |
| `isInteractable` | `boolean` | Returns whether the button can interact with the cursor (click, hover, etc). | Read-Write |

## Functions

| Function Name | Return Type | Description | Tags |
| -------- | ----------- | ----------- | ---- |
| `GetPerkReference()` | [`NetReference`](netreference.md) | Returns a reference to the perk for which this button is currently configured. If no perk has been set, returns an unassigned NetReference. (See the `.isAssigned` property on `NetReference`.) | None |
| `SetPerkReference(NetReference)` | `None` | Configures this button to use the specified perk. | None |

## Events

| Event Name | Return Type | Description | Tags |
| ----- | ----------- | ----------- | ---- |
| `clickedEvent` | [`Event`](event.md)<[`UIPerkPurchaseButton`](uiperkpurchasebutton.md)> | Fired when button is clicked. This triggers on mouse-button up, if both button-down and button-up events happen inside the button hitbox. | None |
| `pressedEvent` | [`Event`](event.md)<[`UIPerkPurchaseButton`](uiperkpurchasebutton.md)> | Fired when button is pressed. (mouse button down) | None |
| `releasedEvent` | [`Event`](event.md)<[`UIPerkPurchaseButton`](uiperkpurchasebutton.md)> | Fired when button is released. (mouse button up) | None |
| `hoveredEvent` | [`Event`](event.md)<[`UIPerkPurchaseButton`](uiperkpurchasebutton.md)> | Fired when button is hovered. | None |
| `unhoveredEvent` | [`Event`](event.md)<[`UIPerkPurchaseButton`](uiperkpurchasebutton.md)> | Fired when button is unhovered. | None |

## Tutorials

[UI in Core](../references/ui.md) | [Implementing Perks](../references/perks/implementing.md)
