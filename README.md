# gmod-nettable2

## Changes to nettable1
- "proto" strings removed

```lua
-- SERVER
local t = {}
t.ply = player.GetAll()[1]
t["lol so rand om xd"] = {
  [1] = 2,
  string = "cool"
}

local nt = nettable2.wrap(t, "some_unique_name")
-- full update is sent on subscription addition
nt:AddSubscription(ply)
nt:AddSubscription({ply table})

-- autosubscription = table is autosent on connection (probs plyinitspawn hook)
nt:SetAutoSubscription(true) -- all players
nt:SetAutoSubscription(function(x) return x:IsAdmin() end) -- admins

nt:AttachMetatable() -- attaches a __newindex hook to base table. this should be only method modifying the base tbl

nt:Update()
```
```lua
-- CLIENT

-- shouldnt have same nettable.get serverside mess anymore so this should be clientside
-- getting the nettable auto-subscribes. __gc should unsubscribe
local nt = nettable2.get("some_unique_name")

-- this is the reference that is to be kept up to date with server
local t = nt.ref

```
