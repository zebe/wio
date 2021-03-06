# Major Changes In Hybrid 6 IRC Server Software

by Josh Rollyson aka Dracus

(minor changes by Joseph Lo aka Jolo)

Last Modified Nov 14, 2002

Ed. note: Hybrid 6 quickly evolved into 7 and then was replaced on EFnet
largely by [ratbox ircd](http://www.ircd-ratbox.org/) [ext. link]. Many of the
following features survived, which is why we are keeping this guide. There is
also a somewhat technical but very thorough [list of differences between
Hybrid 7 and ratbox](http://www.leeh.co.uk/ircd/hyb7-to-ratbox.txt) [ext.
link]. -Jolo 07/22/2004

* * *

## Background

"Hybrid" is the name of a relatively new version of IRC daemon (ircd) server
software. Various versions of hybrid ircd are now in use by a majority of
EFnet servers (36 out of 40, or 90%, as of April 2002). To make things more
complicated, hybrid has evolved from version 6 to 7 as of fall 2002, and other
variants are popping up (e.g., "ratbox"). Hybrid ircd includes many new
features intended to improve the stability of servers and deter abuse,
although there are 2 important caveats:

  1. Whether these changes are effective or even welcome is a matter of some contention. Thus it is not clear if the admins of the remaining servers (which use some flavor of ComStud ircd) will ever follow suit - they've had years to change their minds and haven't yet. 
  2. Until all servers do adopt this new ircd, the new features may not propagate properly from server to server within a network, so the features may work only some of the time on some servers and may fail randomly at any time. 

This "split personality" has caused a lot of confusion and problems with many
IRC clients and scripts. This page will describe some of the new features as
well as suggest solutions to the new problems.

## Finding out if the server you are on runs Hybrid 6

You can test to see if you are on a Hybrid 6 server in several ways.

  1. The 004 numeric indicates the IRCD version in use as well as supported user and channel modes. This numeric is generated only on connect. The latest hybrid versions contain a phrase like "2.8/**hybrid-6**.3(20020217_2)" (note the emphasis). 
  2. The VERSION command shows the IRCD version in use by a server. (for ircII you can simply type `/version`. For other clients you may need to use `/quote VERSION` or `/raw VERSION`.) 
  3. Errors generated by the server. If the hybrid ircd is blocking multiple target commands (which it does by default) you'll get a error when you try to send a PRIVMSG or NOTICE (/msg, /notice in most clients) to more than a few recipients at once. 

# Sample New Features

**Note: Many of the new features are essentially useless, until and unless all EFnet servers adopt the new hybrid-6 ircd.** The old ircd is not compatible with these new changes, and so even if you are on a hybrid-6 server, the commands you issue will not properly propagate across to other servers unless they are also running hybrid-6, and linked up to your server only through other hybrid-6 servers. 

## Multiple Target Commands

**This cripples commands for sending messages to many people at once such as /onotice, /ame, and /amsg which are built into mIRC and many other clients, as well as most popular scripts which provide equivalent features such as /wall.**

Most commands that used to use multiple targets now only allow one target. In
other words you can no longer do `/msg user1,user2,user3 come to
#lamechannel!`. This has **serious** potential to affect users. At present
this is known to affect the following raw IRC commands: NAMES, WHOWAS, NOTICE,
and PRIVMSG (MSG). Note that CTCPs use PRIVMSG and NOTICE.

## @#channel messages and notices

A workaround was implemented for the old problem of multiple target
restrictions (intended to deter mass messaging for spam). If you are an op on
a channel you can /msg @#channel or /notice @#channel to talk to other ops.
You might have to use /quote or /raw to get that to work at present
(especially since ircII used @ as the obsolete DCC TALK). The format is as
follows:

`/quote notice @#channelname :hello world`

The other channel ops will see something like `-yournick- hello world` (like
any other regular notice).

**For mIRC and Ircle users**, you can use the shortcut: 

`/notice @#channelname hello world`

mIRC users can just use this [aliases](/irchelp/mirc/wall6.txt) file to
replace their /ame and /onotice commands (you **MUST** use right mouse to
directly save the file - do not open it with left mouse or the control codes
will be lost). This will work on both the old and new ircd software.

Also note that this functionality has been improved to allow for two more
special forms for a msg/notice. +#channel sends to all voiced users in a
channel. Ops and Voiced users have access to this. @+#channel sends to ops and
voiced, and both ops and voiced users can use it.

Depending on the client, you might not see your own outgoing messages, so
don't flood people when you don't see it :) Finally, if you are using local
channels like &channelname;, just replace the # with & in the above commands.

## Invite through bans/limits

Basically this means on a hybrid 6 server a user can be /invited by an op on
that channel and be allowed in regardless of bans in effect, or limits set. In
the final version and in betas after b48 you will no longer be able to invite
through a ban. You can still invite through a limit.

## Ban Exemptions (+e)

The latest hybrid ircD servers now support ban exceptions. This would allow
the ops to ban a *!*@*host, but still allow specific users from that host,
whom the ops want to continue to allow in the channel. They would be
recognized only by those servers running that version of the hybrid ircD
server software. Currently, not all EFnet servers run hybrid and it is likely
to stay that way indefinitely, so this exception mode may not always work
reliably. Only channel operators are permitted to see the exemption list, and
exemptions take up room in the ban list.

  * USAGE: `/mode #channel +e nick!user@host` &nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;to add an exemption (hostmasks work the same as for bans) 
  * USAGE: `/mode #channel -e nick!user@host` &nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;to remove an exemption 
  * USAGE: `/mode #channel e` &nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;&nbsp_place_holder;to list the exemptions in place on your channel, on that server. 

## Knock! Knock! (/knock)

As of hybrid 6, there is now a facility for gaining entry to an invite only
(+i) or keyword-protected (+k) channel. You can request an invite with /knock
#channelname , subject to the following conditions.

  * The channel must be invite only (+i) or keyword protected (+k) 
  * The channel must not be +s (secret) 
  * You must not be banned from the channel 
  * You may only do one knock every 5 minutes 
  * The server only processes one knock every 10 seconds (per channel ???) 

Under some clients the command may have to be quoted like this:

`/quote knock #channelname` or `/raw knock #channelname`

## NO JOIN ON SPLIT

This server compile-time option makes it so that you can no longer JOIN a
channel on a split server. This makes smurfing servers with this option set
useless, as you can't get ops or get around bans / limits/ keys / invite-only.
Its a shame the Hybrid development team felt that was needed, and even sadder
that admins are having to implement it, but only the smurf pups are to blame
for it. There are other similar options availible, including one which
disables all mode changes during a split. Additionally the server has been
made more secure against potential race conditions during the netjoin by the
addition of a system which detects the real end of a netsplit, and will not
bring the server out of split mode until the split has ended. This means that
without tampering with the server itself, SERVERS CANNOT BE USED TO GAIN OPS
ON CHANNELS.

## G-LINES

Hybrid 6 now supports g-lines (global k-lines). This means once you're banned,
you cannot get on any of the servers on that network which support that
feature. This is found on most (all but a handful) of EFnet servers. The
prevalance on other networks may vary.

## Chanserv/Nickserv

Hybrid 6 has relaxed some traditional restrictions of EFnet IRCDs to provide a
way around TS for servers. This makes the use of Chanserv/Nickserv possible,
but don't expect it on EFnet anytime soon. Do however expect to see it on
smaller networks running Hybrid. A services package called HybServ is
available for Hybrid based networks wishing to run services. Note that as of
2001 EFnet does support pseudo-chanserv through something called
[CHANFIX](chanfix.html).

## Nick/Channel Jupes

Hybrid 6 now supports a way to disable nicknames LOCALLY, by adding a Q: line
for the nick to the server configuration files. This function also can be
applied to channels. Disabled nicknames will not be able to be used. Attempts
to use them will be rejected by the server, either via error messages or via
server generated kills. Channels can also be disabled if JUPE_CHANNEL support
was enabled at compile time on the server. When a channel is disabled users
inside the channel will be silenced as if the +m channelmode was put in force,
even if opped or voiced. No other users will be able to join the channel. This
effect is local only. It will only apply to attempts to access the channel
through the server(s) which has it disabled. Opers may temporarily initiate or
cancel channel jupes with the `/mode #channelname +j` command. Warnings are
delivered to IRC Operators on the server you are on if you attempt to use a
disabled nick or channel.

## New channelmode +d

This is an extended form of channel bans. These work on wildcarded strings,
not hostmask. They will match anything in the users /whois , including
servername, nickname, username, hostname, and realname. Example: /mode
#channelname +d *John*Doe* . Prevents users with "John" and "Doe" anywhere in
a whois field from joining #channelname. This is useful against certain types
of clone attacks, as well as useful for denying access to a channel via a
certian IRC server. Use this with care. Also not that it will not work
completely unless all servers on the net are Hybrid 6.

## Changed meaning for channelmode +p

For hybrid6 channelmode +p now means paranoid instead of private. If a channel
is +p and +i and a channel operator invites someone, all channel operators
will recieve a server generated fake notice informing them of the invite.

## Obtaining more information or obtaining Hybrid

See our [ircd page](index.html) for more information on getting and setting up
Hybrid. As this page will most certanly become outdated, the changelog found
inside the source package is the most authoritative source for information on
changes in Hybrid.

* * *

**For complete information on all Hybrid 6 changes see the [changelog](hybrid6.readme.txt).**
