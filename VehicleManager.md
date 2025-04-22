---
position: 1
title: Vehicle Manager
authors: ArBajt
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

   UnturnedChat.Say(UPlayer, "You entered the vehicle!");
}
```

## OnBeforePlayerConnected
Called before player joins the server.
```csharp
private void HandleBeforeConnect(UnturnedPlayer player)
{
    Logger.Log($"Player {player.CharacterName} is connecting...");
}
```

## OnPlayerDisconnected
Called when player leaves server.
```csharp
private void HandleDisconnect(UnturnedPlayer player)
{
    Logger.Log($"{player.DisplayName} left the server");
}
```

## OnPlayerDamaged
Called when player takes damage.
```csharp
private void HandleDamage(UnturnedPlayer player, ref EDeathCause cause, ref ELimb limb, ref UnturnedPlayer killer, ref Vector3 direction, ref float damage, ref float times, ref bool canDamage)
{
    if (killer != null)
    {
        float totalDamage = damage * times;
        Logger.Log($"{killer.DisplayName} hit {player.DisplayName} for {totalDamage} damage!");
    }
}
```

## OnShutdown
Called when server begins shutdown.
```csharp
private void HandleShutdown()
{
    Logger.Log("Server is shutting down!");
}
```
