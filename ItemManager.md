---
position: 1
title: Item Manager
authors: ArBajt, Mcrow
category: developers
published: true
description: List of events that be used in Rocket plugins from ItemManager class.
---
# Unturned ItemManager Events
List of events that be used in Rocket plugins from [ItemManager]() class.
## Important Notes
- Always subscribe to events in `Load()` method
- Always unsubscribe in `Unload()` to prevent memory leaks
- Event handlers must match the exact signature of the event
- Use `ref` parameters when required by the event signature
- `using Rocket.Core.Plugins;` is required
- `using Rocket.Unturned.Chat;` is required
- `using Rocket.Unturned.Player;` is required
- `using SDG.Unturned;` is required
- `using System;` is required
- `using System.Collections.Generic;` is required
- `using UnityEngine;` is required

## Quick Example
```csharp
public class ExamplePlugin : RocketPlugin
{
  protected override void Load()
  {
    ItemManager.onItemDropAdded += HandleItemDropAdded;
  }

  protected override void Unload()
  {
    ItemManager.onItemDropAdded -= HandleItemDropAdded;
  }

  private void HandleItemDropAdded(Transform model, InteractableItem interactableItem)
  {
    // Get item name
    var itemName = interactableItem.item.GetAsset().itemName;

    // Send message to console with name of the item and position XYZ
    Console.WriteLine($"An item {itemName} was dropped at: [X: {model.transform.position.x}, Y: {model.transform.position.y}, Z: {model.transform.position.z}]");
  }
}
```

## onItemDropRemoved
This event is called when the item is removed from the ground
```csharp
private void HandleItemDropRemoved(Transform model, InteractableItem interactableItem)
{
  // Get item name
  string itemName = interactableItem.item.GetAsset().itemName;

  // Sending message to console with name of the item
  Console.WriteLine($"An item was removed from the ground: {itemName}");
}
```

## onServerSpawningItemDrop
This event is called before the item is welded into the world by the server.
```csharp
private void HandleServerSpawningItemDrop(Item item, ref Vector3 location, ref bool shouldAllow)
{
  // List of blocked item IDs
  List<ushort> blockedItemIDs = new List<ushort>
  {
    363, // Maplestrike
    17, // Military Drum
    15 // Medkit
  };

  // Check if the item being spawned is in the blocked list
  if (blockedItemIDs.Contains(item.id))
  {
    shouldAllow = false; // Deny spawn
    Console.WriteLine($"Blocked item spawn: ID {item.id}");
  }
}
```

## onTakeItemRequested
This event is called when a player tries to pick up an item from the ground and add it to the inventory
```csharp
private void HandleTakeItemRequested(Player player, byte x, byte y, uint instanceID, byte to_x, byte to_y, byte to_rot, byte to_page, ItemData itemData, ref bool shouldAllow)
{
  // Converting variable player from class Player to unturnedPlayer from class UnturnedPlayer
  UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);

  // Check item durability
  byte durability = itemData.item.durability;

  // If the item has less than 50% durability, warn the player
  if (durability < 50)
  {
    UnturnedChat.Say(unturnedPlayer, $"Warning: This item is damaged ({durability}% durability!).", Color.yellow);
  }
}
```
