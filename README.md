# gmod-nettable2

## Changes to nettable1
- "proto" strings removed

## Example code
```lua
-- SERVER
local t = {}
t.ply = player.GetAll()[1]
t["lol so rand om xd"] = {
  [1] = 2,
  string = "cool"
}

local nt = nettable2.Wrap(t, "some_unique_name")
-- full update is sent on subscription addition
nt:Subscribe(ply)
nt:Subscribe({ply table})
nt:Subscribe(ply, "a/sub/path") -- subs to specific path in table

nt:HasSubscription(ply)

nt:Unsubscribe(ply)

-- pvs subscription should be possible as well

-- autosubscription = table is autosent on connection (probs plyinitspawn hook)
nt:SetAutoSubscription(true) -- all players
nt:SetAutoSubscription(function(x) return x:IsAdmin() end) -- admins

-- by default no one is authorized to subscribe (subauth = false)
nt:SetSubscribeAuth(true) -- everyone can subscribe to every path
nt:SetSubscribeAuth(function(ply, path)
  return ply:IsAdmin() -- allow admin to sub to every path
end)

-- by default path is recursively resolved to a table key. behavior can be changed
-- note: all-path is empty string and should be checked for
nt:SetPathResolver(function(t, path)
  return t[path]
end)

nt:AttachMetatable() -- attaches a __newindex hook to base table. this should be only method modifying the base tbl

nt:Update()
```
```lua
-- CLIENT

-- shouldnt have same nettable.get serverside mess anymore so this should be clientside
-- getting the nettable auto-subscribes. __gc should unsubscribe
local nt = nettable2.Get("some_unique_name")

-- this is the reference that is to be kept up to date with server
local t = nt.ref

-- gets a handle to a nettable, but does not subscribe to it
local nt2 = nettable2.GetRaw("another_table")
nt2:Subscribe("a/table/path") -- subscribes to a/table/path key

nt2:Unsubscribe()
```

```lua
-- SHARED
local meta = {}
meta.__index = meta

function meta:GetIndex()
  return self.idx
end

-- metatable identifier is transferred with nettable
-- the 'meta' reference does not have to be the same client and server side
nettable2.RegisterMetaTable("RadioStation", meta)

if SERVER then
  local t = {}
  t.stations = {}
  for i=1, 10 do
    t.stations[i] = setmetatable({idx = i}, meta)
  end
  
  local nt = nettable2.Wrap(t, "stations")
end
if CLIENT then
  local nt = nettable2.Get("stations")
  print(nt.ref[1]:GetIndex())
end
```
