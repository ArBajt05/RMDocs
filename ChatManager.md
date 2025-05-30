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

## onCheckPermissions
This event is called just before command typed by a player in chat is executed
```csharp
private void HandleCheckPermissions(SteamPlayer player, string text, ref bool shouldExecuteCommand, ref bool shouldList)
{
  // Converting variable player from class SteamPlayer to unturnedPlayer from clas UnturnedPlayer
  UnturnedPlayer unturnedPlayer = UnturnedPlayer.FromSteamPlayer(player);

  // Check if the command is /kill and the player doesn't have permission
  if (text.StartsWith("/kill") && !unturnedPlayer.HasPermission("permission.allowkill"))
  {
    // Prevent the command from executing
    shouldExecuteCommand = false;

    // Hide the command from the command list
    shouldList = false;

    // Send a message to the player
    UnturnedChat.Say(unturnedPlayer, "You don't have permission to use this command!", Color.red);
  }
}
```

## onServerFormattingMessage
This event is called before sending a chat message in one of the specified categories such as GLOBAL, GROUP, LOCAL, for example
```csharp
private void HandleServerFormattingChatMessage(SteamPlayer speaker, EChatMode mode, ref string text)
{
  // Add [GROUP CHAT] prefix if the message is sent in GROUP chat mode
  if (mode == EChatMode.GROUP)
  {
    text = "[GROUP CHAT] " + text;
  }

  // Replace the word "fuck" with "This is a very bad word :(" to filter inappropriate language
  text = text.Replace("fuck", "This is a very bad word :(");
}
```
