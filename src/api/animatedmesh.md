---
id: animatedmesh
name: AnimatedMesh
title: AnimatedMesh
tags:
    - API
---

# AnimatedMesh

AnimatedMesh objects are skeletal CoreMeshes with parameterized animations baked into them. They also have sockets exposed to which any CoreObject can be attached.

## Properties

| Property Name | Return Type | Description | Tags |
| -------- | ----------- | ----------- | ---- |
| `animationStance` | `string` | The stance the animated mesh plays. | Read-Write, Client-Only |
| `animationStancePlaybackRate` | `number` | The playback rate for the animation stance being played. | Read-Write, Client-Only |
| `animationStanceShouldLoop` | `boolean` | If `true`, the animation stance will keep playing in a loop. If `false` the animation will stop playing once completed. | Read-Write, Client-Only |
| `playbackRateMultiplier` | `number` | Rate multiplier for all animations played on the animated mesh. Setting this to `0` will stop all animations on the mesh. | Read-Write, Client-Only |

## Functions

| Function Name | Return Type | Description | Tags |
| -------- | ----------- | ----------- | ---- |
| `GetAnimationNames()` | `Array`<`string`> | Returns an array of all available animations on this object. | None |
| `GetAnimationStanceNames()` | `Array`<`string`> | Returns an array of all available animation stances on this object. | None |
| `GetSocketNames()` | `Array`<`string`> | Returns an array of all available sockets on this object. | None |
| `GetAnimationEventNames(string animationName)` | `Array`<`string`> | Returns an array of available animation event names for the specified animation. Raises an error if `animationName` is not a valid animation on this mesh. | None |
| `AttachCoreObject(CoreObject objectToAttach, string socketName)` | `None` | Attaches the specified object to the specified socket on the mesh if they exist. | Client-Only |
| `PlayAnimation(string animationName, [table parameters])` | `None` | Plays an animation on the animated mesh.<br /> Optional parameters can be provided to control the animation playback: `startPosition (number)`: A number between 0 and 1 controlling where in the animation playback will start; `playbackRate (number)`: Controls how fast the animation plays; `shouldLoop (boolean)`: If `true`, the animation will keep playing in a loop. If `false` the animation will stop playing once completed. | Client-Only |
| `StopAnimations()` | `None` | Stops all in-progress animations played via `PlayAnimation` on this object. | Client-Only |
| `GetAnimationDuration(string animationName)` | `number` | Returns the duration of the animation in seconds. Raises an error if `animationName` is not a valid animation on this mesh. | None |
| `GetMeshForSlot(integer slotIndex)` | `string` | Returns the asset ID of the mesh assigned to the specified slot on this `AnimatedMesh`. Returns `nil` if no mesh is assigned to the slot. | Client-Only |
| `SetMeshForSlot(integer slotIndex, string assetId)` | `None` | Assigns a mesh to the specified slot on this `AnimatedMesh`. If `assetId` is an empty string or identifies an incompatible asset, the slot will be cleared. | Client-Only |
| `SetMaterialForSlot(string assetId, string slotName)` | `None` | Set the material in the given slot to the material specified by assetId. | None |
| `GetMaterialSlot(string slotName)` | [`MaterialSlot`](materialslot.md) | Get the MaterialSlot object for the given slot. If called on the client on a networked object, the resulting object cannot be modified. | None |
| `GetMaterialSlots()` | `Array`<[`MaterialSlot`](materialslot.md)> | Get an array of all MaterialSlots on this mesh. If called on the client on a networked object, the resulting object cannot be modified. | None |
| `ResetMaterialSlot(string slotName)` | `None` | Resets a material slot to its original state. | None |

## Events

| Event Name | Return Type | Description | Tags |
| ----- | ----------- | ----------- | ---- |
| `animationEvent` | [`Event`](event.md)<[`AnimatedMesh`](animatedmesh.md) animatedMesh, `string` eventName, `string` animationName> | Some animations have events specified at important points of the animation (e.g. the impact point in a punch animation). This event is fired with the animated mesh that triggered it, the name of the event at those points, and the name of the animation itself. | Client-Only |

## Examples

Example using:

### `animationEvent`

Some animations have events that fire when certain parts of the animations are reached. This allows you to sync up hit effects with animations. Important note! This event is only fired client side. The server cannot directly respond to animation events!

```lua
local propDragonMob = script:GetCustomProperty("DragonMob")
local dragonMesh = World.SpawnAsset(propDragonMob)

function AnimEventListener(mesh, eventName)
    print("Animated Mesh " .. mesh.name .. " just hit event " .. eventName .. "!")
    -- Normally we'd spawn a "Swipe" effect here, and possibly check if we hit a player!
end

dragonMesh.animationEvent:Connect(AnimEventListener)
dragonMesh:PlayAnimation("unarmed_claw")
```

See also: [CoreObject.GetCustomProperty](coreobject.md) | [World.SpawnAsset](world.md) | [AnimatedMesh.PlayAnimation](animatedmesh.md) | [CoreLua.print](coreluafunctions.md)

---

Example using:

### `AttachCoreObject`

Attaches the specified object to the specified socket on the mesh if they exist.

In this example, we want to attach multiple objects to an animated mesh to create a costume, such as equipment on a skeleton enemy or horns on the head of a raptor. For it to work, setup the animated mesh in its "binding" stance and without any animations playing at the start. Place this script along with any costume parts to be attached as children of the animated mesh. Position and rotate the costume parts to align them with their destinations on the mesh. The costume parts are expected to be groups/folders with their names matching the socket names they are destined to. When the script runs, it searches through all the mesh's children and attaches them to the sockets.

```lua
local MESH = script.parent

local allObjects = MESH:GetChildren()

for _, obj in ipairs(allObjects) do
    if obj:IsA("Folder") then
        local socketName = obj.name
        local pos = obj:GetWorldPosition()
        local rot = obj:GetWorldRotation()

        MESH:AttachCoreObject(obj, socketName)

        obj:SetWorldPosition(pos)
        obj:SetWorldRotation(rot)
    end
end
```

See also: [CoreObject.parent](coreobject.md) | [other.IsA](other.md)

---

Example using:

### `GetAnimationNames`

### `GetAnimationStanceNames`

### `GetSocketNames`

### `GetAnimationEventNames`

### `GetAnimationDuration`

You can find out most of the interesting data about an Animated Mesh at runtime, using several handy functions.

```lua
local propDragonMob = script:GetCustomProperty("DragonMob")

-- This function prints out all of the animations, sockets, stances, and events associated
-- with an animated mesh!
function PrintAnimatedMeshData(mesh)
    print("Animation names:")
    for _, v in ipairs(mesh:GetAnimationNames()) do
        print("    " .. v .. "(" .. tostring(mesh:GetAnimationDuration(v)) .. ")")
        -- Print out any events that are associated with this animation:
        for _,e in ipairs(mesh:GetAnimationEventNames(v)) do
            print("        Event: " .. e)
        end
    end
    print("\nAnimation stance names:")
    for _, v in ipairs(mesh:GetAnimationStanceNames()) do
        print("    " .. v)
    end
    print("\nSocket names:")
    for _, v in ipairs(mesh:GetSocketNames()) do
        print("    " .. v)
    end
end

local dragonMesh = World.SpawnAsset(propDragonMob)
PrintAnimatedMeshData(dragonMesh)
```

See also: [CoreObject.GetCustomProperty](coreobject.md) | [World.SpawnAsset](world.md) | [CoreLua.print](coreluafunctions.md)

---

Example using:

### `GetMeshForSlot`

### `SetMeshForSlot`

In this example, a client script copies the mesh slots from one `AnimatedMesh` to another.

```lua
local ANIM_MESH_1 = script:GetCustomProperty("AnimMesh1"):WaitForObject()
local ANIM_MESH_2 = script:GetCustomProperty("AnimMesh2"):WaitForObject()

function Clear(animMesh)
    animMesh:SetMeshForSlot(1, "")
    animMesh:SetMeshForSlot(2, "")
    animMesh:SetMeshForSlot(3, "")
    animMesh:SetMeshForSlot(4, "")
end

function CopyMeshAtSlot(slotIndex)
    local mesh = ANIM_MESH_1:GetMeshForSlot(slotIndex)
    if mesh then
        ANIM_MESH_2:SetMeshForSlot(slotIndex, mesh)
    end
end

Clear(ANIM_MESH_2)

CopyMeshAtSlot(1)
CopyMeshAtSlot(2)
CopyMeshAtSlot(3)
CopyMeshAtSlot(4)
```

See also: [CoreObject.GetCustomProperty](coreobject.md) | [CoreObjectReference.WaitForObject](coreobjectreference.md)

---

Example using:

### `PlayAnimation`

### `playbackRateMultiplier`

Plays an animation on the animated mesh. Optional parameters can be provided to control the animation playback: `playbackRate (Number)`: Controls how fast the animation plays. `shouldLoop (bool)`: If `true`, the animation will keep playing in a loop. If `false` the animation will stop playing once completed.

In this example, a humanoid animated mesh has its laughing and death animations controlled by pressing the primary and secondary action bindings (PC default is mouse left-click and right-click respectively).

```lua
local MESH = script.parent

function PlayAttack()
    MESH:PlayAnimation("unarmed_laugh")
    MESH.playbackRateMultiplier = 1
end

function PlayDeath()
    MESH:PlayAnimation("unarmed_death")
    Task.Wait(1.96)
    -- Prevents the animation from looping or returning to stance
    MESH.playbackRateMultiplier = 0
end

function OnBindingPressed(player, action)
    if action == "ability_primary" then
        PlayAttack()

    elseif action == "ability_secondary" then
        PlayDeath()
    end
end

Game.playerJoinedEvent:Connect(function(player)
    player.bindingPressedEvent:Connect(OnBindingPressed)
end)
```

See also: [CoreObject.parent](coreobject.md) | [Task.Wait](task.md) | [Game.playerJoinedEvent](game.md) | [Player.bindingPressedEvent](player.md) | [Event.Connect](event.md)

---

Example using:

### `StopAnimations`

You can stop whatever animation is currently playing via `StopAnimations()`.

```lua
local propDragonMob = script:GetCustomProperty("DragonMob")
local dragonMesh = World.SpawnAsset(propDragonMob)

dragonMesh:PlayAnimation("unarmed_slash")
Task.Wait(0.25)
dragonMesh:StopAnimations()
```

See also: [CoreObject.GetCustomProperty](coreobject.md) | [World.SpawnAsset](world.md) | [AnimatedMesh.PlayAnimation](animatedmesh.md) | [Task.Wait](task.md)

---

Example using:

### `GetMaterialSlots`

### `SetMaterialForSlot`

In this example an NPC's material is changed at runtime. The script is placed as a child of the NPC's animated mesh and the desired material is assigned to the script as a custom property.

```lua
local ANIM_MESH = script.parent
local MATERIAL = script:GetCustomProperty("Material")

local matSlot = ANIM_MESH:GetMaterialSlots()[1]
ANIM_MESH:SetMaterialForSlot(MATERIAL, matSlot.slotName)
```

See also: [MaterialSlot.slotName](materialslot.md) | [CoreObject.parent](coreobject.md)

---

Example using:

### `animationStance`

### `animationStancePlaybackRate`

### `animationStanceShouldLoop`

The stance the animated mesh plays.

This example demonstrates how to dynamically control the walking stances and to vary their playback speed by how fast the character is moving. This script itself does not move the mesh--that is expected to happen in another script such as an AI or simple `MoveTo()`.

```lua
local MESH = script.parent

-- 0.9 is an approximate scale for the Fox mesh
-- 2 is an approximate for the Raptor and humanoids
local WALK_SCALE = 2

local RUN_BASE = 0.5
local RUN_SCALE = 0.002

-- Thresholds of speed (cm/s) that define which stance to use
local WALKING_SPEED = 15
local RUNNING_SPEED = 300

local lastPos = MESH:GetWorldPosition()

function Tick(deltaTime)
    if deltaTime <= 0 then return end

    local pos = MESH:GetWorldPosition()
    local direction = pos - lastPos
    local speed = direction.size / deltaTime

    -- We can make sure the animation stance loops. If we wanted it to only
    -- play once, we would set it to false here.
    MESH.animationStanceShouldLoop = true

    lastPos = pos

    if speed < WALKING_SPEED then
        MESH.animationStance = "unarmed_idle_ready"

    elseif speed < RUNNING_SPEED then
        MESH.animationStance = "unarmed_walk_forward"
        MESH.animationStancePlaybackRate = WALK_SCALE * (speed - WALKING_SPEED) / (RUNNING_SPEED - WALKING_SPEED)
    else
        MESH.animationStance = "unarmed_run_forward"
        MESH.animationStancePlaybackRate = RUN_BASE + (speed - RUNNING_SPEED) * RUN_SCALE
    end
end
```

See also: [CoreObject.parent](coreobject.md) | [Vector3 - Vector3](vector3.md) | [CoreLua.Tick](coreluafunctions.md)

---
