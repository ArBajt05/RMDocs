---
position: 1
title: Barricade Manager
authors: ArBajt, Mcrow
category: developers
published: true
description: List of events that be used in Rocket plugins from BarricadeManager class.
---
# Unturned BarricadeManager Events
List of events that be used in Rocket plugins from [BarricadeManager]() class.
## Important Notes
- Always subscribe to events in `Load()` method
- Always unsubscribe in `Unload()` to prevent memory leaks
- Event handlers must match the exact signature of the event
- Use `ref` parameters when required by the event signature
- `using Rocket.Core.Plugins;` is required
- `using Rocket.Unturned.Chat;` is required
- `using Rocket.Unturned.Player;` is required
- `using SDG.Unturned;` is required
- `using Steamworks;` is required
- `using UnityEngine;` is required

## Quick Example
```csharp
public class ExamplePlugin : RocketPlugin<ExampleConfiguration>
{
    protected override void Load()
    {
        BarricadeManager.OnRepaired += HandlePlayerOnRepairing;
    }

    protected override void Unload()
    {
        BarricadeManager.OnRepaired -= HandlePlayerOnRepairing;
    }

    private void HandlePlayerOnRepairing(CSteamID instigatorSteamID, Transform barricadeTransform, float totalHealing)
    {
        // Convert the CSteamID to an UnturnedPlayer class variable
        UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigatorSteamID);

        // Informs the player that the barricade is being repaired with information on how much hp it is being repaired for 
        UnturnedChat.Say(unturnedPlayer ,$"You repaired the barricade for {totalHealing} HP.!", Color.green);
    }
}
```

## OnRepairRequested
This event is called when player is trying to repair a barricade.
```csharp
private void HandlePlayerRepairRequested(CSteamID instigatorSteamID, Transform barricadeTransform, ref float pendingTotalHealing, ref bool shouldAllow)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigatorSteamID);

    pendingTotalHealing = 25f; // Healing set at 25 hp

    // Informs the player that the barricade is being repaired with information on how much hp it is being repaired for 
     UnturnedChat.Say(unturnedPlayer, $"You repair a barricade with {pendingTotalHealing} hp", Color.green);
}
```

## onDamageBarricadeRequested
This event is called when player is trying to make a damage to barricade.
```csharp
private void HandlePlayerDamageBarricadeRequest(CSteamID instigatorSteamID, Transform barricadeTransform, ref ushort pendingTotalDamage, ref bool shouldAllow, EDamageOrigin damageOrigin)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigatorSteamID);

    // Check if the source of damage is NOT a Rocket explosion
    if (damageOrigin != EDamageOrigin.Rocket_Explosion)
    {
        shouldAllow = true; // Allow damage from all sources except Rocket explosions
    }
    else
    {
        shouldAllow = false; // Disallow damage if the damage comes from a Rocket explosion
        UnturnedChat.Say(unturnedPlayer, "You cannot damage barricades with Rocket explosions!"); // Inform the player that Rocket explosions cannot damage barricades
    }
}
```

## onDeployBarricadeRequested
This event is called when player is placing a barricade.
```csharp
private void HandlePlayerDeployBarricadeRequest(Barricade barricade, ItemBarricadeAsset asset, Transform hit, ref Vector3 point, ref float angle_x, ref float angle_y, ref float angle_z, ref ulong owner, ref ulong group, ref bool shouldAllow)
{
    // Convert the owner SteamID (ulong) to an UnturnedPlayer class variable
    UnturnedPlayer player = UnturnedPlayer.FromCSteamID(new CSteamID(owner));

    // Checks if the barricade being placed is a Metal Wardrobe (ID 1281)
    if (asset.id == 1281)
    {
        // Automatically rotate the barricade with id 1281(Metal Waredrobe) to 90 degrees on the Y axis
        angle_y += 90f;
        UnturnedChat.Say(player, "You placed a barricade"); // Informs the player that he has placed a barricade.
    }
}
```

## onOpenStorageRequested
This event is called when player opens a storage
```csharp
private void HandlePlayerOpenStorageRequested(CSteamID instigator, InteractableStorage storage, ref bool shouldAllow)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigator);

    // Finds the storage based on it's transform
    BarricadeDrop drop = BarricadeManager.FindBarricadeByRootTransform(storage.transform);

    // Check if the player is the owner of the storage
    if (drop.GetServersideData().owner != unturnedPlayer.CSteamID.m_SteamID)
    {
        shouldAllow = false; // Deny access
        UnturnedChat.Say(unturnedPlayer, "You are not the owner of this storage!", Color.red);
    }
    else
    {
        shouldAllow = true; // Allow access
        UnturnedChat.Say(unturnedPlayer, "You opened your storage.", Color.green);
    }
}
```

## onTransformRequested
This event is called when player is trying to transform the barricade for example in Edit Mode(Shift + F1, then Shift + F6)
```csharp
private void HandlePlayerTransformBarricadeRequested(CSteamID instigator, byte x, byte y, ushort plant, uint instanceID, ref Vector3 point, ref byte angle_x, ref byte angle_y, ref byte angle_z, ref bool shouldAllow)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer player = UnturnedPlayer.FromCSteamID(instigator);

    // Get the barricade region at the specified map location
    BarricadeRegion region = BarricadeManager.regions[x, y];

    // Search for the barricade in this region using the instance ID
    BarricadeDrop targetedBarricade = region.drops.FirstOrDefault(d => d.instanceID == instanceID);

    // Check if the player is the owner of the barricade
    if (targetedBarricade.GetServersideData().owner != player.CSteamID.m_SteamID)
    {
        // If the player is not the owner, block the transformation and rotation of the barricade
        shouldAllow = false;
        UnturnedChat.Say(player, "You are not the owner of this barricade!", Color.red);
    }
    else
    {
        // If the player is the owner, allow the transformation and rotation of the barricade
        shouldAllow = true;
        UnturnedChat.Say(player, "You moved your barricade.", Color.green);
    }
}
```

## onBarricadeSpawned
This event is called when the barricade will be put up
```csharp
private void HandlePlayerBarricadeSpawned(BarricadeRegion region, BarricadeDrop drop)
{
    // Get the SteamID of the player who owns the barricade.
    ulong owner = drop.GetServersideData().owner;

    // Convert the owner SteamID (ulong) to an UnturnedPlayer class variable
    UnturnedPlayer player = UnturnedPlayer.FromCSteamID(new CSteamID(owner));

    // Get the barricade ID
    ushort assetId = drop.asset.id;

    // Send a message to the player confirming the barricade placement
    UnturnedChat.Say(player, $"You placed a barricade with ID: {assetId}", Color.cyan);
}
```
