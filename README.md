# obscure-information
Repository of dumb things Garry's Mod

----

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
