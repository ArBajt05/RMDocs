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
- `using Steamworks;` is required
- `using SDG.Unturned;` is required
- `using Rocket.Unturned.Player;` is required
- `using Rocket.Core.Plugins;` is required

## Quick Example
```csharp
public class ExamplePlugin : RocketPlugin<ExampleConfiguration>
{
    protected override void Load()
    {
        VehicleManager.onEnterVehicleRequested += HandlePlayerEnterVehicle;
    }

    protected override void Unload()
    {
        VehicleManager.onEnterVehicleRequested -= HandlePlayerEnterVehicle;
    }

    private void HandlePlayerEnterVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow)
    {
        // Converting variable player from class Player to unturnedPlayer from clas UnturnedPlayer
        UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);

        Logger.Log($"{unturnedPlayer.DisplayName} has entered the vehicle!");
    }
}
```

## onEnterVehicleRequested
This event is called when player is trying to enter the vehicle.
```csharp
private void HandlePlayerEnterVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow)
{
    // Converting variable player from class Player to unturnedPlayer from clas UnturnedPlayer
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player); // Allows action

    UnturnedChat.Say(unturnedPlayer, "You entered the vehicle!");
}
```

## onExitVehicleRequested
This event is called when player is trying to exit the vehicle.
```csharp
private void HandlePlayerExitVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow, ref Vector3 pendingLocation, ref float pendingYaw)
{
   // Converting variable player from class Player to unturnedPlayer from clas UnturnedPlayer
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);

    UnturnedChat.Say(unturnedPlayer, "You exited the vehicle!");
}
```

## onSwapSeatRequested
This event is called when player is trying to swap seats in the vehicle.
```csharp
private void HandlePlayerSwapSeatInVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow, byte fromSeatIndex, ref byte toSeatIndex)
{
   // Converting variable player from class Player to unturnedPlayer from clas UnturnedPlayer
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);

    // Informs the player of the seat change in the car
    if (toSeatIndex == 0) // To driver seat
    {
        UnturnedChat.Say(unturnedPlayer, "You moved to the driver's seat");
    }
    else if (fromSeatIndex == 0) // From driver seat
    {
        UnturnedChat.Say(unturnedPlayer, "You moved from the driver's seat to another");
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
