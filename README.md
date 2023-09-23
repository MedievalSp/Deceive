![Deceive Logo](http://i.thijsmolendijk.nl/deceive.png)

[![Discord](https://discordapp.com/api/guilds/249481856687407104/widget.png?style=shield)](https://discord.gg/bfxdsRC)

# :tophat: Deceive

## This is a fork! find the original deceive in https://github.com/molenzwiebel/Deceive all credits to the owner.

# Download the modified exe here: https://drive.google.com/uc?id=1An6ICPxGdCFop-CFkDPbFe90ZoHyYpQD

Deceiver has nothing to do with the laugnage configuration. Deceiver is only meant to hide you from your friends (Appear offline). For the language settings follow the steps below. It works with or without Deceiver as of September 2023.

I found a workaround to make Deceiver work and keep the language settings... It turns out that what forces the languages overrides in LeagueClientSettings.yaml is the dumb RiotClient.exe launching the LeagueClient.exe

So the idea is simple: let Deceive launch and hook to RiotClient.exe WITHOUT the game parameter, then the code launches a LeagueClient.exe instance. Not very stylish, but this works fine on my end.

Currently, the only workaround that works to have your Client language different than your forced region language is to:

1. Log in to your server
2. Close the client without signing out
3. Go to `<your Riot Games folder>`/League of Legends/Config
4. Open LeagueClientSettings.yaml in a text editor
5. Replace the value in the locale line globals with the value that corresponds to your designated language (see table below) for example:
```
...
      globals:
        locale: "en_US"
        region: "LA1"
...

  ```
7. Make sure the value in the region line matches the region code of your server (see table here https://nemo.gg/blog/change-the-language-and-voiceover-in-lol/).
8. Start the League of Legends client using RiotClient.exe. DO NOT OPEN League of Legends using RiotClientServices.exe or your default desktop shortcut. It will override the settings and change you back to the region language. Always use RiotClient.exe or this version of Deceiver.

### Fork modifications:
From Line 135 in StartupHandler.cs:

```
 // Step 4: Launch Riot Client (+game)
        var startArgs = new ProcessStartInfo { FileName = riotClientPath, Arguments = $"--client-config-url=\"http://127.0.0.1:{proxyServer.ConfigPort}\"" };

        // *** Modification Starts *** //
        // This argument causes RiotClient to autostart LOL causing custom languages settings to get blown up.
        //if (launchProduct is not null)
        //    startArgs.Arguments += $" --launch-product={launchProduct} --launch-patchline={gamePatchline}";

        //Pass any custom params used in deceive.exe --riot-client-params=""
        if (riotClientParams is not null)
            startArgs.Arguments += $" {riotClientParams}";

        // Pass any custom params used in deceive.exe --game-params=""
        if (gameParams is not null)
            startArgs.Arguments += $" -- {gameParams}";

        Trace.WriteLine($"About to launch Riot Client with parameters:\n{startArgs.Arguments}");
        var riotClient = Process.Start(startArgs);

        // Get LeagueClient.exe path
        var LeagueClientPath = riotClientPath.Replace("Riot Client/RiotClientServices.exe", "League of Legends/LeagueClient.exe");

        // Nasty, but the only way I could get this to work was to wait for the actual Riot Client to complete bootstrapping
        await Task.Delay(2000);

        // Launch LeagueClient manually without any params. This method preserves the language settings in LeagueClientSettings.yaml
        Process.Start(LeagueClientPath);
        
        // *** Modification Ends *** //

        // Kill Deceive when Riot Client has exited, so no ghost Deceive exists.
        if (riotClient is not null)
        {
            ListenToRiotClientExit(riotClient);

        }
```

Deceive allows you to appear offline in League of Legends, VALORANT and Legends of Runeterra without any loss of functionality! Talk to your friends, communicate in champion select and queue up together, all while sneakily appearing offline to all your friends.

Once started, Deceive will be a little icon in your notification tray that allows you to manage your chat presence, whether it be online, offline, or moblie.

# FAQ

**Where can I download Deceive?**  
Click the [Releases](https://github.com/molenzwiebel/Deceive/releases) tab at the top to download the latest version.

**Can I still invite people? Can they invite me?**  
Your friends list will work as normal, which means that you can invite everyone. Your friends will not be able to invite you, even if they enter your name manually.

**Can I talk in lobbies/champion/agent select?**  
Yes, you can talk in lobbies just fine. Only your global "presence" is filtered.

**How do I use Deceive with a specific game?**  
The first time you launch Deceive, you will be able to choose which game to launch and whether to remember that decision. You can also use the Deceive tray icon to launch a different game.

You can also launch Deceive with `lol`, `lor`, or `valorant` as command-line argument to automatically launch your game of choice.

**Is this approved by Riot?**  
Riot has confirmed that [you won't get banned](https://i.thijsmolendijk.nl/deceive_ok.png) for using Deceive. It may break at any time though.

**I'm more of a visual learner. Do you have a video?**  
Sure thing! Just click the preview below:  
[![Youtube Preview](http://img.youtube.com/vi/bfsbtd39GqE/maxresdefault.jpg)](https://youtu.be/bfsbtd39GqE)
