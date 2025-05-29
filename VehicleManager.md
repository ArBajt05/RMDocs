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

## onDamageVehicleRequested
This event is called when a player tries to inflict damage to a vehicle
```csharp
private void HandleVehicleDamageRequest(CSteamID instigatorSteamID, InteractableVehicle vehicle, ref ushort pendingTotalDamage, ref bool canRepair, ref bool shouldAllow, EDamageOrigin damageOrigin)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigatorSteamID);

    // Check if the damage comes from Melee
    if (damageOrigin == EDamageOrigin.Useable_Melee)
    {
        shouldAllow = false; // Block the damage
        UnturnedChat.Say(unturnedPlayer, "You cannot damage vehicles using Melee weapons!", Color.red);
    }
    else
    {
        // Allow damage and set a custom amount of damage
        shouldAllow = true;
        pendingTotalDamage = 50;

        // Inform the player about the damage dealt
        UnturnedChat.Say(unturnedPlayer, $"You dealt {pendingTotalDamage} damage to a vehicle named '{vehicle.asset.name}'", Color.green);
    }
}
```

## onRepairVehicleRequested
This event is called When a player tries to repair a vehicle
```csharp
private void HandleVehicleRepairRequested(CSteamID instigatorSteamID, InteractableVehicle vehicle, ref ushort pendingTotalHealing, ref bool shouldAllow)
{
    // Convert the CSteamID to an UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromCSteamID(instigatorSteamID);

    // Check if the player is the owner of the vehicle
    if (vehicle.lockedOwner == unturnedPlayer.CSteamID)
    {
        // Allow the repair and set the healing amount
        shouldAllow = true;
        pendingTotalHealing = 20;

        // Notify the player
        UnturnedChat.Say(unturnedPlayer, $"You repaired your vehicle for {pendingTotalHealing} HP.", Color.green);
    }
    else
    {
        // Deny the repair if the player is not the owner
        shouldAllow = false;
        UnturnedChat.Say(unturnedPlayer, "You are not the owner of this vehicle and cannot repair it!", Color.red);
    }
}
```

## onSiphonVehicleRequested
This event is called when a player tries to drain fuel from a vehicle
```csharp
private void HandleVehicleSiphonRequested(InteractableVehicle vehicle, Player instigatingPlayer, ref bool shouldAllow, ref ushort desiredAmount)
{
    // Convert the Player class to UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(instigatingPlayer);

    // Check if the vehicle has a specific ID
    if (vehicle.asset.id == 10)
    {
        // Deny siphoning fuel from this specific vehicle
        shouldAllow = false;

        // Inform the player that this vehicle cannot be siphoned
        UnturnedChat.Say(unturnedPlayer, "You can't siphon fuel from a vehicle you don't own or share a group with!", Color.red);
    }
    else
    {
        // Allow siphoning and set a fixed amount of fuel to siphon
        shouldAllow = true;
        desiredAmount = 40;

        // Inform the player that they successfully siphoned fuel
        UnturnedChat.Say(unturnedPlayer, $"You siphoned {desiredAmount} fuel from the vehicle.", Color.green);
    }
}
```

## onVehicleCarjacked
This event is called when a player tries to use carjack on the vehicle
```csharp
private void HandleVehicleCarjack(InteractableVehicle vehicle, Player instigatingPlayer, ref bool allow, ref Vector3 force, ref Vector3 torque)
{
    // Convert the Player class to UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(instigatingPlayer);

    // Check if the vehicle is locked to prevent unauthorized carjacking
    if (vehicle.isLocked)
    {
        allow = false; // Deny carjack
        UnturnedChat.Say(unturnedPlayer, "This vehicle is locked! You can't carjack it.", Color.red);

        // Apply a small knockback force and torque to the vehicle as a feedback effect
        force = new Vector3(0, 5f, -10f);   // Push the vehicle slightly backward and upward
        torque = new Vector3(0, 2f, 0);     // Slight spin effect
    }
    else
    {
        allow = true; // Allow carjack
        UnturnedChat.Say(unturnedPlayer, "You successfully carjacked the vehicle.", Color.green);
    }
}
```

## onVehicleLockpicked
This event is called when a player attempts to lockpick the vehicle
```csharp
private void HandleVehicleLockpicked(InteractableVehicle vehicle, Player instigatingPlayer, ref bool allow)
{
    // Convert the Player class to UnturnedPlayer class variable
    UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromPlayer(instigatingPlayer);

    // Check if the vehicle is already unlocked
    if (!vehicle.isLocked)
    {
        // If the vehicle is unlocked, deny the lockpick attempt
        allow = false;
        UnturnedChat.Say(unturnedPlayer, "This vehicle is already unlocked.", Color.red);
    }
    else
    {
        // If the vehicle is locked, allow the lockpicking action
        allow = true;
        UnturnedChat.Say(unturnedPlayer, "You are lockpicking the vehicle.", Color.green);
    }
}
```
