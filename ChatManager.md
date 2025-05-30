---
position: 1
title: Chat Manager
authors: ArBajt, Mcrow
category: developers
published: true
description: List of events that be used in Rocket plugins from ChatManager class.
---
# Unturned ChatManager Events
List of events that be used in Rocket plugins from [ChatManager]() class.
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
public class ExamplePlugin : RocketPlugin
{
  protected override void Load()
  {
      ChatManager.onChatted += HandlePlayerChatted;
  }

  protected override void Unload()
  {
      ChatManager.onChatted -= HandlePlayerChatted;
  }

  private void HandlePlayerChatted(SteamPlayer player, EChatMode mode, ref Color chatted, ref bool isRich, string text, ref bool isVisible)
  {
    // Check if the player typed "unturned"
    if (text.ToLower() == "unturned")
    {
        // Converting variable player from class SteamPlayer to unturnedPlayer from clas UnturnedPlayer
        UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromSteamPlayer(player);

        // Sending a message to the player
        UnturnedChat.Say(unturnedPlayer, "Nelson fix this game please!", Color.green);

        // Hide the original message from global chat which is "unturned"
        isVisible = false;
    }  
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
