* The bot has only one *target channel* to merge others into.

# "User" (channel owner) side

1. Channel owner adds the bot to their channel with the right to view messages.
   i. The authorization check runs (it may be hardcoded/configurable whitelist,
      for example)
2. All new posts from the channel are synced.

When the bot is removed from the channel, the syncing stops.

# Internal behavior

* The *main bot* has a pool of user(bot)s that it can use, swapping them out in
  case of ban
  - Each user has some amount of *child bots*, tied to it
    + Upon creation, child bots are added to the target channel
    + Child bots send the message to target channel from other channels (e.g.
      using copyMessage BotAPI method)
* The main bot maintains a database of all the messages being synced, and keeps
  them in sync
  - The main bot edits the messages, when they're edited in the original channel
  - The main bot deletes the messages, when they're deleted in the original
    channel
    + The main bot must regularly check, whether any synced messages got
      deleted (most recent messages should be, probably, checked more
      frequently)
* When the main bot is added to the channel and authorization check passes,
  currently active userbot creates a child bot, that will send the messages
  from that channel
* When currently active userbot gets banned, a new one "spins up", joins the
  target channel (e.g. via invite link) and recreates all the child bots needed
