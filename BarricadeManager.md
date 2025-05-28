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

## OnToggleVehicleLockRequested
This event is called when player locks or unlocks the vehicle
```csharp
private void HandlePlayerToggleVehicleLock(InteractableVehicle vehicle, ref bool shouldAllow)
{
    // Finds the player in the driver's seat (seat 0) and converts it from the Player class to UnturnedPlayer
    Player player = vehicle.passengers[0].player.player;
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);

    // Event starts before vehicle.isLocked changes value
    // Checks if the vehicle is locked
    if (vehicle.isLocked && vehicle.isDriver)
    {
        UnturnedChat.Say(unturnedPlayer, "You opened the vehicle");
    }
    else
    {
        UnturnedChat.Say(unturnedPlayer, "You closed the vehicle");
    }
}
```

## onDamageTireRequested
This event is called when attempting to damage a vehicle's tire
```csharp
private void HandleVehicleTireDamage(CSteamID instigatorSteamID, InteractableVehicle vehicle, int tireIndex, ref bool shouldAllow, EDamageOrigin damageOrigin)
{
    // If the source of damage is a melee weapon we block tire damage
    if (damageOrigin == EDamageOrigin.Useable_Melee)
    {
        // We block tire damage
        shouldAllow = false;
    }
    else
    {
        // In other cases such as firearms, for example, we allow damage to the tire
        shouldAllow = true;
    }
}
```
