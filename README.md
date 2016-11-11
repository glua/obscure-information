# obscure-information
Repository of dumb things Garry's Mod

----

## General

#### [Windows] [Client?] Use junction instead of symlink for addon folders  
Seems to work better (ie. models in `addons/mylink/models` load when using junction but not when using a symlink).


## API

#### `GM` is available on first load, `GAMEMODE` on subsequent loads
Can be worked around with something like `local GM = GM or GAMEMODE`

> Meepen Ƹ̵̡Ӝ̵̨̄Ʒ: [you should] not do that

#### Drawing a custom mesh with lighting requires rendering a model in the draw hook
Source: https://facepunch.com/showthread.php?t=1456688&p=47356770&viewfull=1#post47356770

#### If entity does not appear when rendering an entity manually, call `SetupBones` 
For instance:
```lua
e:SetupBones()
e:SetRenderOrigin(somepos)
e:DrawModel()
```

#### Pre/Post opaque/translucent hooks are each called twice per frame
Namely once for 3d skybox entities, once after (see https://wiki.garrysmod.com/page/Render_Order).
If you do something expensive in the hooks that only needs to be done once per frame, it might be worth it to return early in one of the passes.

#### Rendering custom stuff to a env_projectedtexture
If you set the texture of env_projectedtexture to some string (eg. `myprojtex`), you can render to a RT clientside with same name to change what's displayed on the projectedtexture.

#### HDR bloom/overexposure in RTs workaround
Makes the rt look more like what you're actually drawing to it.
```lua
local nilToneMappingScale = Vector(1, 1, 1)
```
```lua
local oldTMSL = render.GetToneMappingScaleLinear()
render.SetToneMappingScaleLinear(nilToneMappingScale)
-- Draw to RT here
render.SetToneMappingScaleLinear(oldTMSL)
```

#### SetSubMaterial silently fails if material is not loaded
Source: https://github.com/Facepunch/garrysmod-issues/issues/1511, https://facepunch.com/showthread.php?t=1374457&p=46073827&viewfull=1#post46073827

You can load the material by using `surface.SetMaterial(mat)` anywhere on client.

#### `Panel:SetDisabled` is placebo Garry code
If you actually want to disable Panel functionality, use `Panel:SetEnabled(false)`.

#### Creating a brush entity
Source: https://facepunch.com/showthread.php?t=1529285&p=50927618&viewfull=1#post50927618
Credits: wauterboi & rubat
```lua
AddCSLuaFile();

ENT.Base  = "base_anim";
ENT.Type  = "anim";

ENT.RenderGroup = RENDERGROUP_TRANSLUCENT;

function ENT:Initialize()
  if (SERVER) then
    self:SetMoveType(MOVETYPE_VPHYSICS);
    self:SetSolid(SOLID_VPHYSICS);
    self:SetModel(self.Model);
  else
    self:SetRenderBounds(self:OBBMins(), self:OBBMaxs());
  end
end

function ENT:Draw()
  self:DrawModel();
end

function ENT:KeyValue(key, value)
  if (key == "model") then self.Model = value end
end
```

#### RenderTarget name truncation  
`GetRenderTarget` seems to truncate the name to x characters. If you experience render targets pointing to same buffer even with different names, try to shorten the used names to see if it's related to truncation.

#### Transparent rendertargets  
Source: https://facepunch.com/showthread.php?t=1497293&p=49312072&viewfull=1#post49312072
```lua
local nm = "myrt"

local rt = GetRenderTargetEx( nm, 512, 512, RT_SIZE_NO_CHANGE, MATERIAL_RT_DEPTH_NONE, 8, 4, IMAGE_FORMAT_RGBA8888 )
render.PushRenderTarget(rt)
render.OverrideAlphaWriteEnable( true, true )
render.ClearDepth()
render.Clear(0, 0, 0, 0)
render.OverrideAlphaWriteEnable( false )
render.PopRenderTarget()

-- Note: specifying alphatest, vertexcolor in material constructor seems to be important
local mat = CreateMaterial(nm, "VertexLitGeneric", {["$alphatest"] = "1", ["$vertexcolor"] = "1"})
mat:SetTexture("$basetexture", rt)
```

#### NextBot animations  
If using activities other than `ACT_WALK` or `ACT_RUN`, remember to edit BodyUpdate (https://github.com/garrynewman/garrysmod/blob/master/garrysmod/gamemodes/base/entities/entities/base_nextbot/sv_nextbot.lua#L64)

#### 3D sounds played via BASS must be mono  
