---
position: 1
title: Vehicle Manager
authors: ArBajt, Mcrow
category: developers
published: true
description: List of events that be used in Rocket plugins from VehicleManager class.
---
# Unturned VehicleManager Events
List of events that be used in Rocket plugins from [VehicleManager]() class.
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
        // Converting variable player from class Player to UPlayer from clas UnturnedPlayer
        UnturnedPlayer UPlayer = UnturnedPlayer.FromPlayer(player);

        Logger.Log($"{UPlayer.DisplayName} has entered the vehicle!");
    }
}
```

## onEnterVehicleRequested
Called when player is trying to enter the vehicle.
```csharp
private void HandlePlayerEnterVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow)
{
   // Converting variable player from class Player to UPlayer from clas UnturnedPlayer
      UnturnedPlayer UPlayer = UnturnedPlayer.FromPlayer(player);

    if (UPlayer == null)
    {
        shouldAllow = false;
        return;
    }

    if (vehicle == null)
    {
        shouldAllow = false;
        return;
    }

    UnturnedChat.Say(UPlayer, "You entered the vehicle!");

    shouldAllow = true;
}
```

## onExitVehicleRequested
Called when player is trying to exit the vehicle.
```csharp
private void HandlePlayerExitVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow, ref Vector3 pendingLocation, ref float pendingYaw)
{
   // Converting variable player from class Player to UPlayer from clas UnturnedPlayer
    UnturnedPlayer UPlayer = UnturnedPlayer.FromPlayer(player);

    if (UPlayer == null)
    {
        shouldAllow = false;
        return;
    }

    if (vehicle == null)
    {
        shouldAllow = false;
        return;
    }

    shouldAllow = true;
    UnturnedChat.Say(UPlayer, "You exited the vehicle!");
}
```

## onSwapSeatRequested
Called when player is trying to swap seats in the vehicle.
```csharp
private void HandlePlayerSwapSeatInVehicle(Player player, InteractableVehicle vehicle, ref bool shouldAllow, byte fromSeatIndex, ref byte toSeatIndex)
{
   // Converting variable player from class Player to UPlayer from clas UnturnedPlayer
    UnturnedPlayer UPlayer = UnturnedPlayer.FromPlayer(player);

    if (UPlayer == null)
    {
        shouldAllow = false;
        return;
    }

    if (vehicle == null)
    {
        shouldAllow = false;
        return;
    }

    if (toSeatIndex == 0)
    {
        UnturnedChat.Say(UPlayer, "You moved to the driver's seat");
    }
    else if (fromSeatIndex == 0)
    {
        UnturnedChat.Say(UPlayer, "You moved from the driver's seat to another");
    }

    shouldAllow = true;
}
```

## OnToggleVehicleLockRequested
Called when player Toggle Vehicle Lock
```csharp
private void HandlePlayerToggleVehicleLock(InteractableVehicle vehicle, ref bool shouldAllow)
{
    UnturnedPlayer UPlayer = Provider.clients.Select(c => UnturnedPlayer.FromSteamPlayer(c)).FirstOrDefault(up => up.Player.movement.getVehicle() == vehicle);
    if (UPlayer == null)
    {
        shouldAllow = false;
        return;
    }
    shouldAllow = true;

    if (vehicle.isLocked)
    {
        UnturnedChat.Say(UPlayer, "You opened the vehicle");
    }
    else
    {
        UnturnedChat.Say(UPlayer, "You closed the vehicle");
    }
}
```
