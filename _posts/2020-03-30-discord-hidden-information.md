---
title: FireShell CTF 2020 - Against the Perfect discord Inquisitor 1 and 2
layout: post 
date: '2020-03-30 10:20:00'
description: Writeup of Against the Perfect discord Inquisitor 1 and 2
image: 'https://i.imgur.com/IwVgujt.jpg'
tags:
- ctf
- writeup
- misc
author: k4l1
---

Hi everyone! C:

Discord is one of mosts used communication software in the world that is supported on multiple platforms. There you can create servers, channels, and a lot of things like create bots. You can use this application via web/client or API.

Some informations shouldn't be seen for any user on any plataform, but you can see with API or edited client, like `hidden channels`. On FireShell CTF 2020 I made two challenges that you need to manage these things and get the flag.


## MISC Challenges - Against the Perfect discord Inquisitor 1 & 2

### First description
>You're on a journay and becomes to the Tavern of a >Kingdom Enemy, you need to get information of a secret >organization for the next quest. Be careful about the >Inquisitor! he can ban you from this world.
>TL;DR find the flag
>{:.blockquote}

### Second description

>Has a mage on tavern that reveals secrets from place. He is friendly, so he can help you!
>Be careful about the Inquisitor! he can ban you from this world.
>TL;DR use the bot to get the flag
>{:.blockquote}

### Server: [Kingdom Chall](https://discord.gg/fHHyU6g)

Hints: Title, 2FA may not work.

This Discord server has basically one channel called `tavern`. You need to pay attention about the chall name that revels API on uppercase and search about how access hidden informations. You need to get the hidden channel `hidden-round-table` to both challs, the first flag is the Topic and the second flag you'll get with the bot sending the last message id of this channel.

Has so many ways to solve:
1. Edited Client(BetterDiscord)
2. Official Discord API
3. Self-bot
4. Client Web + BurpSuite

2FA/OAuth may not work because the Token.

You need to be careful about the Discord Policy. Self-bots, edited client and some requests via Discord API can verify your e-mail or ban you, this occur trying something like list all members on server with:

```bash
$ curl -sH "Authorization: $TOKEN" https://discordapp.com/api/v6/guilds/{guild.id}/members | jq
```

### BetterDiscord

BetterDiscord is a edited client that you can do a lot of cool stuffs like custom css, plug-ins and custom themes. For a long time Discord trying to deny this unofficial client updating the application to turn useless and threatening to ban users that use that. Btw, you can use this client with `Show hidden channels` plug-in to solve this chall, as this video:

[![](https://i.ytimg.com/vi/-COfkwjVEyY/hqdefault.jpg?sqp=-oaymwEZCNACELwBSFXyq4qpAwsIARUAAIhCGAFwAQ==&rs=AOn4CLDQJJwJF7bjMk4RFU-BPiv05QS35w)](https://www.youtube.com/watch?v=-COfkwjVEyY)

### Self-Bot

Self-bot also is a hard thing because Discord dislike that, for this reason self-bots in JS Discord lib was deprecated, only on Python lib worked for me.

I made the script below to get the hidden channel and print the topic and last_message_id:

```py
import discord
import asyncio
from discord.ext import commands

bot = commands.Bot(command_prefix='$', self_bot=True)

@bot.event
async def on_ready():
  try:
    print("Ready :)")
    guild = bot.get_guild(688190172793536536)
    for channel in guild.channels:
      if channel.type.name == "text":
        print(channel.topic) #First Challenge
        print(channel.last_message_id) #Second Challenge
  except Exception as ex:
    print(ex)
    bot.close()

def main():
    bot.run("Your Token", bot=False)

if __name__ == "__main__":
    main()
```

The script can be a little slow because Discord Policy about self-bots.

### Discord API

You can request the Discord API via cURL or with `request` python lib like this script: [Writeup by lyellread](https://github.com/lyellread/ctf-writeups/blob/master/2020-fireshell/discord-1-and-2/README.md). (Btw, nice writeup dude! C: )

```bash
export TOKEN="Your Token"
```
#### First Chall
```bash
$ curl -sH "Authorization: $TOKEN" https://discordapp.com/api/v6/guilds/688190172793536536/channels | jq
[
    //...
    "topic": "F#{The_Table_of_King_Arthur}",
    //...
]
```
#### Second Chall
```bash
$ curl -sH "Authorization: $TOKEN" https://discordapp.com/api/v6/guilds/688190172793536536/channels | jq
[
    //...
    "last_message_id": "688214063595258088",
    //...
]
```
After that, use Gandalf Bot to get the Second flag:

![](https://i.imgur.com/danf5N9.png)

```bash
$ echo -n "RiN7UzRiM1JfMTVfVGgzX0sxbmdfQXJ0aHVyfQ==" | base64 -d
F#{S4b3R_15_Th3_K1ng_Arthur}
```


## References

* [Accessing Discord hidden information](https://lucasnathaniel.github.io/discord-hidden-information/)
* [Discord Oficial API](https://discordapp.com/developers/docs/reference)
* [Python Discord API](https://discordpy.readthedocs.io/en/latest/)
* [BetterDiscord](https://betterdiscord.net/)