# Friends Privacy

## TL;DR
- **Your friends** have access to your raid clears and your masteries ([why masteries?](#why-the-masteries)).
- **Your friends** get a daily subtoken with restricted access to clear data. If you
  revoke their access, they will lose access within one day.
- The **friends server** also knows a few extras that might be added in the future
  (dungeons, worldbosses and achievements), your account name, and some extra [account data](https://wiki.guildwars2.com/wiki/API:2/account).

There are two subtoken sharing settings:
- *Everyone* – anyone may request a subtoken with your clear data (everyone is your friend),
- *Friends only* – only players you select get access to your clear data.

## Detailed description
Through the magic of [GW2 API
subtokens](https://wiki.guildwars2.com/wiki/API:2/createsubtoken), the **friends server** has access to the following, and nothing else:
- `/v2/tokeninfo` – used to verify token validity,
- `/v2/account` – used to access account name, this is never shared further to avoid impersonation,
- `/v2/account/masteries` – used to access the `last_modified` information with minimal information leaks about the account,
- `/v2/account/raids` – used to access raid clear status,
- `/v2/account/achievements` – may be used in the future to add features that require checking achievements,
- `/v2/account/dungeons` – may be used in the future for dungeon clears,
- `/v2/account/worldbosses` – may be used in the future for world boss clears (unlikely),
- `/v2/createsubtoken` – used to further restrict information when sharing to friends.

**Your friends** get short-term subtokens of the server's subtoken and their access is limited to:
- `/v2/account/raids` – raid clear data
- `/v2/account/masteries` – used to get the time of the last account data update; this is needed to correctly handle raid clear resets

Notably, the subtoken that is shared with friends does *not* give any access to
the account name, so it cannot be used to impersonate you.

The subtokens shared with your friends have an expiration of less than **1
day** from creation. If you revoke their access to the data, they will not be
get a new subtoken, which means they lose access within one day. This is
required as the only other way to revoke access is to delete the entire API
key.

## Why the masteries
The raid clear data in the GW2 API does *not* get updated if you are offline.
If there was a raid reset since the last update, it will still say that you
finished some encounters until you log in.

For this reason, we need to know the last modification date of the clear data
to properly show your clears. For that, we need to do a request to another
endpoint. We chose the masteries endpoint because it reveals the least
information of all the options. It doesn't allow for impersonation, as there is
no account name available.
