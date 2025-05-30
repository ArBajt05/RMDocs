---
position: 1
title: Useable Tire
authors: ArBajt, Mcrow
category: developers
published: true
description: List of events that be used in Rocket plugins from UseableTire class.
---
# Unturned UseableTire Events
List of events that be used in Rocket plugins from [UseableTire]() class.
## Important Notes
- Always subscribe to events in `Load()` method
- Always unsubscribe in `Unload()` to prevent memory leaks
- Event handlers must match the exact signature of the event
- Use `ref` parameters when required by the event signature
- `using Rocket.Core.Plugins;` is required
- `using Rocket.Unturned.Chat;` is required
- `using Rocket.Unturned.Player;` is required
- `using SDG.Unturned;` is required

## Quick Example
```csharp
public class ExamplePlugin : RocketPlugin
{
  protected override void Load()
  {
    UseableTire.onModifyTireRequested += HandleOnModifyTireRequested;
  }

  protected override void Unload()
  {
    UseableTire.onModifyTireRequested -= HandleOnModifyTireRequested;
  }

  private void HandleOnModifyTireRequested(UseableTire useable, InteractableVehicle vehicle, int wheelIndex, ref bool shouldAllow)
  {
    // Converting CSteamID to UnturnedPlayer class
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(useable.player.channel.owner.playerID.steamID);

    /* or you can use this instead to convert:
    *    Player player = useable.player;
    *    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(player);
    */

    // Prevent removing or attaching tires on locked vehicles
    if (vehicle.isLocked)
    {
      // Deny action and inform a player
      shouldAllow = false;
      UnturnedChat.Say(unturnedPlayer, "This vehicle is locked. You can't modify its tires");
    }
    else
    {
      // Allow action
      shouldAllow = true;
    }
  }
}
```

## onModifyTireRequested
This event is called when a player tries to mount or remove a tire on a vehicle.
```csharp
private void HandleOnModifyTireRequested2(UseableTire useable, InteractableVehicle vehicle, int wheelIndex, ref bool shouldAllow)
{
  // Converting variable from class Player to unturnedPlayer from class UnturnedPlayer
  UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(useable.player);

  // Count how many tires are still on the vehicle
  int remainingTires = 0;
  for (int i = 0; i < vehicle.tires.Length; i++)
  {
    if (vehicle.tires[i].isAlive)
    {
      remainingTires++;
    }
  }

  // Inform the player
  UnturnedChat.Say(unturnedPlayer, $"This vehicle has {remainingTires} tires remaining.", UnityEngine.Color.green);
}
```
