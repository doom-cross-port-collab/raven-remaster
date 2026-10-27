# Raven EX Specification

## Things / actors

### Properties

| Name              | Type        | Description |
|-------------------|-------------|-------------|
| doomednum         | integer     | |
| spawnstate        | state       | |
| seestate          | state       | |
| painstate         | state       | |
| meleestate        | state       | |
| missilestate      | state       | |
| crashstate        | state       | |
| deathstate        | state       | |
| xdeathstate       | state       | |
| icedeathstate     | state       | |
| activatestate     | state       | |
| deactivatestate   | state       | |
| raisestate        | state       | |
| stunstate         | state       | |
| customstates      | object      | \[XA] TODO: document |
| seesound          | sound       | |
| activesound       | sound       | |
| attacksound       | sound       | |
| ripsound          | sound       | |
| painsound         | sound       | |
| deathsound        | sound       | |
| activatesound     | sound       | |
| deactivatesound   | sound       | |
| flags             | actor flags | |
| addflags          | actor flags | |
| removeflags       | actor flags | |
| health            | integer     | |
| reactiontime      | integer     | |
| painchance        | integer     | |
| speed             | decimal     | |
| radius            | decimal     | |
| height            | decimal     | |
| mass              | integer     | |
| damage            | integer     | |
| damagedice        | integer     | |
| fastspeed         | decimal     | |
| meleerange        | decimal     | |
| infightinggroup   | integer     | |
| projectilegroup   | integer     | |
| splashgroup       | integer     | |
| minrespawntics    | integer     | |
| minrespawnseconds | decimal     | |
| respawndice       | integer     | |
| pickuptype        | pickup      | |
| translationlump   | string      | |
| selfdamagefactor  | decimal     | |
| behavior          | behavior    | mobj behavior; see "Behaviors" tab for valid values & what they do |
| dropitem          | dropitem    | |
| dropitem2         | dropitem    | |
| automapoffsetx    | integer     | |
| automapoffsety    | integer     | |

### Flags

| Name                | Origin   | Description |
|---------------------|----------|-------------|
| SPECIAL             | Vanilla  | |
| SOLID               | Vanilla  | |
| SHOOTABLE           | Vanilla  | |
| NOSECTOR            | Vanilla  | |
| NOBLOCKMAP          | Vanilla  | |
| AMBUSH              | Vanilla  | |
| JUSTHIT             | Vanilla  | |
| JUSTATTACKED        | Vanilla  | |
| SPAWNCEILING        | Vanilla  | |
| NOGRAVITY           | Vanilla  | |
| DROPOFF             | Vanilla  | |
| PICKUP              | Vanilla  | |
| NOCLIP              | Vanilla  | |
| FLOAT               | Vanilla  | |
| TELEPORT            | Vanilla  | |
| MISSILE             | Vanilla  | |
| DROPPED             | Vanilla  | |
| SHADOW              | Vanilla  | Actor is translucent _and_ a ghost. Effectively a combo of GZDoom's SHADOW flag, GHOST flag, and Renderstyle "Translucent" using the main translucency percent values (i.e. in GZDoom, HR_SHADOW and HX_SHADOW) |
| NOBLOOD             | Vanilla  | |
| CORPSE              | Vanilla  | |
| INFLOAT             | Vanilla  | |
| COUNTKILL           | Vanilla  | |
| COUNTITEM           | Vanilla  | |
| SKULLFLY            | Vanilla  | |
| NOTDMATCH           | Vanilla  | |
| TRANSLATION         | Reserved | N/A -- reserved, but unimplemented |
| TRANSLATION2        | Reserved | N/A -- reserved, but unimplemented |
| TOUCHY              | MBF      | Actor dies when touched; same semantics as GZDoom |
| BOUNCES             | Reserved | N/A -- reserved, but unimplemented |
| FRIEND              | Reserved | N/A -- reserved, but unimplemented |
| TRANSLUCENT         | Boom     | Actor is translucent, using the main translucency percent values (i.e. in GZDoom, HR_SHADOW and HX_SHADOW) -- this flag does not imply projectile inaccuracy or ghost behavior. |
| LOGRAV              | Vanilla  | alternate gravity setting |
| WINDTHRUST          | Vanilla  | gets pushed around by the wind specials |
| FLOORBOUNCE         | Vanilla  | bounces off the floor |
| THRUGHOST           | Vanilla  | missile will pass through ghosts |
| FLY                 | Vanilla  | fly mode is active |
| FLOORCLIP           | Vanilla  | if feet are allowed to be clipped |
| SPAWNFLOAT          | Vanilla  | spawn random float z |
| NOTELEPORT          | Vanilla  | does not teleport |
| RIP                 | Vanilla  | missile rips through solid targets |
| PUSHABLE            | Vanilla  | can be pushed by other moving mobjs |
| SLIDE               | Vanilla  | slides against walls (e.g. GZDoom's SLIDESONWALLS flag) |
| ONMOBJ              | Vanilla  | mobj is resting on top of another mobj |
| PASSMOBJ            | Vanilla  | Enable z block checking.  If on, this flag will allow the mobj to pass over/under other mobjs. |
| CANNOTPUSH          | Vanilla  | cannot push other pushable mobjs |
| SLIDEONACTORS       | New      | slides against other actors. in vanilla, Hexen actors have both SLIDE and SLIDEONACTORS, while Heretic just has SLIDE -- does GZDoom disambiguate? |
| BOSS                | Vanilla  | mobj is a major boss (lots of internal checks on this for various shenanigans; should be same as GZDoom) |
| FIREDAMAGE          | Vanilla  | does fire damage |
| NODMGTHRUST         | Vanilla  | does not thrust target when damaging |
| TELESTOMP           | Vanilla  | mobj can stomp another |
| FLOATBOB            | Vanilla  | use float bobbing z movement |
| DONTDRAW            | Vanilla  | don't generate a vissprite |
| IMPACT              | Vanilla  | an MF_MISSILE mobj can activate SPAC_IMPACT |
| PUSHWALL            | Vanilla  | mobj can push walls |
| MCROSS              | Vanilla  | can activate monster cross lines; same as GZDoom's "ACTIVATEMCROSS" |
| PCROSS              | Vanilla  | can activate projectile cross lines; same as GZDoom's "ACTIVATEPCROSS" |
| CANTLEAVEFLOORPIC   | Vanilla  | stay within a certain floor type |
| NONSHOOTABLE        | Vanilla  | mobj is totally non-shootable, but still considered solid |
| INVULNERABLE        | Vanilla  | mobj is invulnerable |
| DORMANT             | Vanilla  | thing is dormant |
| ICEDAMAGE           | Vanilla  | does ice damage |
| SEEKERMISSILE       | Modified | is a seeking missile -- in H+H, internally this flag is only checked when reflecting/parrying a missile (i.e. to set its tracer to its target) |
| REFLECTIVE          | Vanilla  | reflects missiles |
| ALTSHADOW           | Vanilla  | Actor is translucent, using the alternative translucency percent values (i.e. in GZDoom, HX_ALTSHADOW) -- this flag does not imply projectile inaccuracy or ghost behavior. |
| ICECORPSE           | Vanilla  | Actor is a frozen corpse |
| BLASTED             | Vanilla  | Bbject has been blasted by the disc of repulsion; deals damage on impact |
| DMGIGNORED          | MBF21    | Other things ignore its attacks (archvile) |
| NORADIUSDMG         | MBF21    | Doesn't take splash damage (cyberdemon, mastermind) |
| FORCERADIUSDMG      | MBF21    | Thing causes splash damage even if the target shouldn't |
| RANGEHALF           | MBF21    | Use half distance for missile attack probability (cyberdemon, mastermind, revenant, lost soul) |
| FULLVOLSOUNDS       | MBF21    | Full volume see / death sounds (cyberdemon, mastermind) |
| NORESPAWN           | MBF21    | Disables nightmare respawns |
| SPECIALSTAYS_SINGLE | Reserved | N/A -- reserved, but unimplemented |
| SPECIALSTAYS_COOP   | Reserved | N/A -- reserved, but unimplemented |
| SPECIALSTAYS_DM     | Reserved | N/A -- reserved, but unimplemented |
| FULLVOLACTIVE       | New      | play active sounds at full volume; used by D'Sparil and Hexen bosses |
| TRANSFERVELOCITY    | Reserved | N-A -- transfer velocity to any objects that are standing on this object; formerly hardcoded to MT_POD -- H+H eventually shipped with this disabled 'cause it sucked :P |
| FLOORMISSILE        | New      | projectile hugs the floor and can travel over any steps (e.g. minotaur floor fire attack, mage lightning) |
| CEILINGMISSILE      | New      | ditto, for ceilings (e.g. mage lightning) |
| INVULNCHARGE        | New      | monster is immune to damage while charging (i.e. when MF_SKULLFLY flag set) |
| DONTMORPH           | New      | monster is immune to being morphed into a chicken/pig |
| FASTMISSILE         | Internal | Not exposed to EXDEFS -- actor is a fast missile; this is just used for some internal bookkeeping in H+H; other ports probably don't care. just listing it for completeness |
| WALLBOUNCE          | New      | projectile bounces off walls. Hexen's counterpart to Heretic's MF2_FLOORBOUNCE. |
| NOSPLASH            | New      | cannot trigger terrain splash effects when hitting the ground, regardless of mass |
| NOMELEEGHOST        | New      | monster is unable to melee attack ghosts. |
| NOINFIGHTING        | New      | monster will never infight with another monster. |
| DRAWONAUTOMAP       | New      | actor's sprite is drawn on the automap -- drawing works just like AutomapMarker in GZDoom, effectively |
| TOGGLEDORMANT       | New      | actor's MF2_DORMANT flag will be toggled when calling Thing Activate/Deactivate on it; previously hardcoded to MF_COUNTKILL -- GZDoom may have different semantics here? |
| PSETRANS            | New      | draw translucent if the "PSE translucency" menu option is enabled -- this may be N/A for ports unless they want to also add this feature to the menu (it's a quick thing to wire up). |
| USESPECIAL          | New      | actor will execute its special when the player presses the Use button while facing it. |
| THRUICECORPSE       | New      | missile will pass through ice corpses |
| SHIELDPARRY         | Internal | Not directly exposed to EXDEFS -- Cleric shield parry: actors with a valid stunstate that perform a melee attack against an actor with SHIELDPARRY will jump to their stunstate and have the STUNNED flag set. |
| STUNNED             | Internal | Not directly exposed to EXDEFS -- Actor is stunned, and takes extra damage from certain melee attacks (e.g. mace). This flag is cleared in A_Chase. |
| NOSRCDMGTHRUST      | New      | Do not thrust when damage is dealt by an attack whose source (not inflictor) has this flag; this is set on Hexen playerclasses, to prevent damage-based thrust for all attacks. |
| FOILINVUL           | New      | missiles with this flag will ignore invulnerability on monsters (but not players) |
| DONTBLAST           | New      | cannot be blasted by the disc of repulsion |
| STAYMORPHED         | New      | monsters with this flag will never unmorph |
| NONETSYNC           | New      | Object is never synchronized on the network (i.e. clientside-only) |
| RANDOMACTIVESOUND   | New      | actor will randomly play its seesound instead of its activesound, 50% of the time (previously hardcoded to Wizard/Bishop/Pig) |
| NOFLYOVER           | New      | actors with this flag will not attempt to fly over each other even if the PASSMOBJ flag is set (previously hardcoded to Gargoyle/Wizard) |

## States

### Properties

| Name        | Type         | Description |
|-------------|--------------|-------------|
| sprite      | sprite       | |
| frame       | frame        | \[XA] TODO: document special cases |
| tics        | integer      | |
| seconds     | decimal      | |
| next        | state        | \[XA] TODO: document special cases |
| flags       | state flags  | |
| addflags    | state flags  | |
| removeflags | state flags  | |
| action      | state action | |
| args        | args         | |
| offsetx     | integer      | |
| offsety     | integer      | |

### Flags

| Name          | Origin | Description |
|---------------|--------|-------------|
| SKILL5FAST    | MBF21  | Frame duration is halved on Skill 5 |
| INTERPOLATE   | New    | Interpolate weapon offsets (i.e. misc1/misc2 fields) -- Note for GZDoom: H+H state offset interpolation is a lerp over the entire duration of the state, not just 1 tic |
| NOINTERPOLATE | New    | Do not interpolate weapon offsets, even if weapon has the WF_INTERPOLATE flag set |

## Player class

### Properties

| Name              | Type                   | Description |
|-------------------|------------------------|-------------|
| actor             | actor                  | |
| displayname       | string                 | |
| flags             | playerclass flags      | |
| addflags          | playerclass flags      | |
| removeflags       | playerclass flags      | |
| spawnfilter       | mapthing flags         | |
| speedfactor       | decimal                | |
| forwardwalkspeed  | integer                | |
| forwardrunspeed   | integer                | |
| forwardairspeed   | decimal                | |
| sidewalkspeed     | integer                | |
| siderunspeed      | integer                | |
| sideairspeed      | decimal                | |
| powerspeedfactor  | decimal                | |
| speedtrailactor   | actor                  | |
| viewheight        | decimal                | |
| jumpvelocity      | decimal                | |
| actorstepheight   | decimal                | |
| invulncolormap    | string                 | |
| sounds            | playerclass.sounds     | |
| states            | playerclass.states     | |
| actions           | playerclass.actions    | |
| hexenarmor        | playerclass.hexenarmor | |
| menubackground    | string                 | |
| menusprite        | string                 | |
| menunumframes     | integer                | |
| oof               | sound                  | |
| grunt             | sound                  | |
| falling           | sound                  | |
| noway             | sound                  | |
| keytry            | sound                  | |
| puzzlefail        | sound                  | |
| pain              | sound                  | |
| death             | sound                  | |
| wimpydeath        | sound                  | |
| crazydeath        | sound                  | |
| gibdeath1         | sound                  | |
| gibdeath2         | sound                  | |
| gibdeath3         | sound                  | |
| idle              | state                  | |
| run               | state                  | |
| attack1           | state                  | |
| attack2           | state                  | |
| firedeath         | state                  | |
| firedeathend      | state                  | |
| flechette         | artifact action        | |
| healingradius     | artifact action        | |
| healingradiusargs | args                   | |
| invulnstart       | player action          | |
| invulntick        | player action          | |
| invulndraw        | playervis action       | |
| invulnend         | player action          | |
| max               | integer                | |
| base              | integer                | |
| piecearmor        | integer                | |
| pieceshield       | integer                | |
| piecehelmet       | integer                | |
| pieceamulet       | integer                | |

### Flags

| Name           | Origin | Description |
|----------------|--------|-------------|
| MENUSELECTABLE | New    | Whether or not this class is selectable via new game menu |
| WEAPONBOB      | New    | Allow weapon bobbing |
| WEAPONLOWER    | New    | Allow weapon lowering |
| AUTOUSEHEALTH  | New    | Allow auto-use health |
| CHICKENTWITCH  | New    | Does involuntary chicken actions, such as twitching, jumping, and making noise. |

## Weapon

### Properties

| Name                 | Type         | Description |
|----------------------|--------------|-------------|
| ammo                 | ammo         | |
| ammo2                | ammo         | |
| selectstate          | state        | |
| deselectstate        | state        | |
| readystate           | state        | |
| firestate            | state        | |
| holdstate            | state        | |
| altfirestate         | state        | |
| altholdstate         | state        | |
| flashstate           | state        | |
| customstates         | object       | |
| flags                | weapon flags | |
| addflags             | weapon flags | |
| removeflags          | weapon flags | |
| ammopershot          | integer      | |
| ammopershot2         | integer      | |
| slot                 | integer      | |
| slotpriority         | integer      | |
| switchpriority       | integer      | |
| initialowned         | integer      | |
| initialraised        | integer      | |
| carouselicon         | string       | |
| fullscreenoffset     | integer      | |
| playerclass          | playerclass  | |
| numpieces            | integer      | |
| pickupmessage        | string       | |
| piecepickupmessage   | string       | |
| pieceassemblymessage | string       | |
| weaponlevel2         | weapon       | |

### Flags

| Name            | Origin   | Description |
|-----------------|----------|-------------|
| NOTHRUST        | MBF21    | Damage dealt by this weapon will not thrust victims; formerly hardcoded to Gauntlets |
| SILENT          | MBF21    | Weapon does not alert monsters when firing |
| NOAUTOFIRE      | MBF21    | |
| FLEEMELEE       | MBF21    | |
| AUTOSWITCHFROM  | MBF21    | |
| NOAUTOSWITCHTO  | MBF21    | |
| EXTREMEDEATH    | New      | always gib; formerly hardcoded to Hexen's 4th weapon slot. Implemented as a weapon flag for compatibility with vanilla Hexen semantics |
| FORCETOREADY    | New      | force weapon back to the ready state when Tome of Power expires; formerly hardcoded to Heretic Staff & Gauntlets |
| PHOENIXRESET    | New      | force weapon back into ready state and consume 1 unit of ammo when Tome of Power expires; formerly hardcoded to Phoenix Rod |
| DEPOWERSWITCH   | New      | lower & re-raise the weapon on Tome of Power expiration; formerly hardcoded to Heretic Staff & Gauntlets |
| AMMOOPTIONAL    | New      | ammo is optional for this weapon (i.e. can initiate an attack even at zero ammo); formely hardcoded to Fighter Axe and Hammer |
| SPECIALISOFFSET | Internal | special handling for Heretic's chicken beak vertical offsets. This is a weird internal Kex thing; I wouldn't expect this to do anything normal elsewhere :P |
| INTERPOLATE     | New      | interpolate weapon x/y offsets (misc1/misc2 fields) on all states (unless said state has NOINTERPOLATION on it) -- basically a shortcut to enable interpolation by default without redefining everything |
| NODMGTHRUST     | New      | [XA] TODO: this is a silly partial duplicate of MF_NOTHRUST; I need to remove this Kex-side :P |
| AUTOAIMFARTHEST | New      | autoaim prioritizes the farthest enemy, rather than the nearest |

## Ammo

### Properties

| Name                 | Type    | Description |
|----------------------|---------|-------------|
| clipammo             | integer | |
| maxammo              | integer | |
| initialammo          | integer | |
| maxupgradedammo      | integer | |
| boxammo              | integer | |
| backpackammo         | integer | |
| weaponammo           | integer | |
| weaponpieceammo      | integer | |
| deathmatchweaponammo | integer | |
| skill1multiplier     | decimal | |
| skill2multiplier     | decimal | |
| skill3multiplier     | decimal | |
| skill4multiplier     | decimal | |
| skill5multiplier     | decimal | |
| icon                 | string  | |

## Puff

### Properties

| Name            | Type       | Description |
|-----------------|------------|-------------|
| thingtype       | actor      | |
| sound           | sound      | |
| misssound       | sound      | |
| hitsound        | sound      | |
| hitpufftype     | actor      | |
| nobloodpufftype | actor      | |
| upspeed         | decimal    | |
| zspread         | decimal    | |
| flags           | puff flags | |
| addflags        | puff flags | |
| removeflags     | puff flags | |

### Flags

| Name          | Origin   | Description |
|---------------|----------|-------------|
| PUFFONACTORS  | New      | always puff on actors -- NOTE: this behavior is a bit redundant with the hitpufftype field; this flag is defined for parity with EE |
| BLOODLESS     | New      | don't spawn blood when hitting a bleeding target |
| LOCALTHRUST   | New      | apply damage thrust from puff's position, not shooter's |
| RANDOMTICS    | New      | randomize first frame duration |
| TARGETSHOOTER | Reserved | set puff's target to the shooter, like projectiles -- NOTE: this behavior is always enforced; this flag is defined for parity with EE |
| ALTBLOOD      | New      | use alternative blood splat (formerly hardcoded to the Fighter Axe) |

## Pickup

### Properties

| Name        | Type          | Description |
|-------------|---------------|-------------|
| sound       | sound         | |
| message     | string        | |
| flashtics   | integer       | |
| action      | pickup action | |
| args        | args          | |
| flags       | pickup flags  | |
| addflags    | pickup flags  | |
| removeflags | pickup flags  | |

### Flags

| Name            | Origin | Description |
|-----------------|--------|-------------|
| ALWAYSPICKUP    | New    | always pick up this item, even if the player already has everything it attempts to grant |
| MULTIPLAYERSTAY | New    | pickup mobj stays behind on pickup in multiplayer; overrules ALWAYSPICKUP in if both are set. |
| WEAPONSTAY      | New    | pickup mobj stays behind on pickup if the multiplayer gamemode uses "weapon stay" semantics (many ports have explicit options for this; in vanilla, this means non-altdeath deathmatch) |
| ARTIFLASH       | New    | pickup mobj plays the artifact flash animation on pickup, instead of removing the mobj. |
| INSTANCED       | New    | pickup is instanced for each player in multiplayer modes -- this is a Kex multiplayer feature; something nice to have for other MP ports but they gotta implement it first |

## Power

| Name        | Origin | Description |
|-------------|--------|-------------|
| MORPHIMMUNE | New    | power grants immunity to being morphed |
| MORPHRESET  | New    | remove this power when being morphed |
| CHANGERESET | New    | remove this power when voluntarily changing classes (e.g. via the "Hexen Chronicle" in the enhanced maps) |

### Properties

| Name                  | Type         | Description |
|-----------------------|--------------|-------------|
| durationtics          | integer      | |
| durationseconds       | decimal      | |
| blinkthresholdtics    | integer      | |
| blinkthresholdseconds | decimal      | |
| renewthresholdtics    | integer      | |
| renewthresholdseconds | decimal      | |
| startaction           | power action | |
| tickaction            | power action | |
| endaction             | power action | |
| flags                 | power flags  | |
| addflags              | power flags  | |
| removeflags           | power flags  | |

## Artifact

### Properties

| Name        | Type            | Description |
|-------------|-----------------|-------------|
| displayname | string          | |
| icon        | string          | |
| maxamount   | integer         | |
| carryover   | integer         | |
| usesound    | sound           | |
| action      | artifact action | |
| args        | args            | |
| flags       | artifact flags  | |
| addflags    | artifact flags  | |
| removeflags | artifact flags  | |

### Flags

| Name                | Origin | Description |
|---------------------|--------|-------------|
| AUTOUSE_HEALTH_BABY | New    | automatically-used health on baby difficulty |
| AUTOUSE_HEALTH_DM   | New    | automatically-used health artifact on deathmath |

## Lock

### Properties

| Name              | Type    | Description |
|-------------------|---------|-------------|
| lockdoormessage   | string  | |
| lockswitchmessage | string  | |
| keypickupmessage  | string  | |
| keyicon           | string  | |
| color             | integer | |

## Sound

### Properties

| Name     | Type           | Description |
|----------|----------------|-------------|
| lump     | string         | |
| alias    | string         | |
| tactile  | sound.tactile  | |
| channel  | channel flags  | |
| position | position flags | |
| volume   | integer        | |

## Ambient

### Properties

| Name      | Type    | Description |
|-----------|---------|-------------|
| doomednum | integer | |
| sequence  | object  | \[XA] TODO: document |

## Terrain

### Properties

| Name           | Type          | Description |
|----------------|---------------|-------------|
| floorclip      | decimal       | |
| flags          | terrain flags | |
| addflags       | terrain flags | |
| removeflags    | terrain flags | |
| sound          | sound         | |
| baseactor      | actor         | |
| chunkactor     | actor         | |
| chunkxvelrange | decimal       | |
| chunkyvelrange | decimal       | |
| chunkzvelrange | decimal       | |
| chunkzvelbase  | decimal       | |
| smallactor     | actor         | |
| smallclip      | decimal       | |
| smallsound     | sound         | |
| damage         | integer       | |
| damageflags    | damage flags  | |
| damagetics     | integer       | |
| damageseconds  | decimal       | |
| damagesound    | sound         | |

### Flags

| Names       | Origin | Description |
|-------------|--------|-------------|
| LIQUID      | New    | terrain is a liquid; floor-bouncing missiles will remove themselves when impacting this surface. |
| LOWFRICTION | New    | terrain is low friction (i.e. ice physics) |
| ALERT       | New    | alerts monsters if a player triggers the splash |

## Behaviors

| Name                   | Description                                     | Effects |
|------------------------|-------------------------------------------------|---------|
| player_skull           | Player gib skull                                | |
| player_speed_trail     | Player speed trail                              | |
| blaster_missile        | Dragon Claw tomed projectile                    | |
| blaster_ripper         | Dragon Claw tomed ripper ball                   | |
| skullrod_seeker        | Hellstaff tomed projectile                      | |
| skullrod_rain          | Hellstaff rain projectiles                      | |
| phoenixrod_missile     | Phoenix Rod projectile                          | |
| phoenixrod_flame       | Phoenix Rod tomed projectile (flamethrower)     | |
| mace_spawner           | Firemace spawn spot                             | |
| mace_deathball         | Firemace tomed projectile ("death ball")        | |
| key_gizmo_blue         | Blue Key Gizmo                                  | |
| key_gizmo_green        | Green Key Gizmo                                 | |
| key_gizmo_yellow       | Yellow Key Gizmo                                | |
| fighter_hammer_missile | Hammer of Retribution projectile                | |
| fighter_sword_missile  | Quietus projectile                              | |
| cleric_staff_missile   | Serpent Staff projectile                        | |
| cleric_flame_circle    | Firestorm circling flames                       | |
| holy_spirit            | Wraithverge spirit                              | |
| mage_frost_missile     | Frost Shards projectile                         | |
| mage_lightning_ceiling | Mage Lightning ceiling projectile               | |
| mage_lightning_floor   | Mage Lightning floor projectile                 | |
| mage_lightning_zap     | Mage Lightning vertical zap projectile          | |
| mage_staff_oldmissile  | Old (unused) Bloodscourge projectile            | |
| mage_staff_missile     | Bloodscourge projectile                         | |
| firebomb               | Time Bomb / Mage Flechette                      | |
| egg_missile            | Morph Ovum projectile                           | |
| pork_missile           | Porkalataor projectile                          | |
| telother_missile       | Banishment Device projectile                    | |
| telother_spot          | Banishment Device landing spot (new to Medusa)  | |
| mummy_missile          | Nitrogolem projectile                           | |
| knight_axe_green       | Undead Warrior green axe                        | |
| knight_axe_red         | Undead Warrior red axe                          | |
| ironlich               | Iron Lich                                       | |
| minotaur               | Maulotaur                                       | |
| minotaur_friend        | Friendly Maulotaur (Dark Servant)               | |
| minotaur_spread_missile| Maulotaur spread attack missile                 | |
| minotaur_floor_missile | Maulotaur floor missile                         | |
| dsparil_serpent_missile| D'Sparil serpent fireball                       | |
| dsparil_dismounted     | D'Sparil (dismounted)                           | |
| dsparil_missile        | D'Sparil magic missile                          | |
| whirlwind              | Iron Lich whirlwind projectile                  | |
| ettin                  | Ettin                                           | |
| centaur                | Centaur/Slaughtaur                              | |
| demon_green            | Green Chaos Serpent                             | |
| heresiarch             | Heresiarch                                      | |
| heresiarch_orbiter1    | Heresiarch orbiting ball 1                      | |
| heresiarch_orbiter2    | Heresiarch orbiting ball 2                      | |
| heresiarch_orbiter3    | Heresiarch orbiting ball 3                      | |
| heresiarch_bouncer     | Heresiarch bouncing projectile                  | |
| heresiarch_missile     | Heresiarch purple exploding missile             | |
| fighter_boss           | Zedek (Fighter Boss)                            | |
| cleric_boss            | Traductus (Cleric Boss)                         | |
| mage_boss              | Menelkir (Mage Boss)                            | |
| iceguy                 | Wendigo                                         | |
| iceguy_missile_shard   | Wendigo projectile shards                       | |
| stalker_leader         | Stalker Leader                                  | |
| stalker_missile        | Stalker missile                                 | |
| bishop                 | Dark Bishop                                     | |
| bishop_missile         | Dark Bishop projectile                          | |
| wraith_buried          | Buried Reiver                                   | |
| dragon_explosion       | Death Wyvern projectile explosion               | |
| poison_dart            | Poison dart trap projectile                     | |
| poison_cloud           | Poison cloud (cleric flechette / mushroom)      | |
| tree_destructible      | Destructible tree                               | |
| tree_burnable          | Burnable-only trees (XMas tree and large shrub) | |
| lynched_heartless      | Hexen lynched heartless corpse                  | |
| thrust_spike           | Hexen thrust spike traps                        | |
| pottery                | Hexen pottery                                   | |
| bell                   | Hexen bell                                      | |
| glass_shard            | Stained glass shard                             | |
| quake_focus            | Earthquake focus                                | |
| blast_effect           | Disc of Repulsion blast effect                  | |
| teleport_destination   | Teleport destination                            | |
| bridge                 | Bridge thing that spawns glitter objects        | |

## Codepointer Overview

| Name                   | Type   | Origin            | Description |
|------------------------|--------|-------------------|-------------|
| A_Light0               | Weapon | Vanilla (Common)  | |
| A_Light1               | Weapon | Vanilla (Common)  | |
| A_Light2               | Weapon | Vanilla (Common)  | |
| A_WeaponReady          | Weapon | Vanilla (Common)  | |
| A_Lower                | Weapon | Vanilla (Common)  | |
| A_Raise                | Weapon | Vanilla (Common)  | |
| A_ReFire               | Weapon | Vanilla (Common)  | |
| A_Look                 | Actor  | Vanilla (Common)  | |
| A_Chase                | Actor  | Vanilla (Common)  | |
| A_FaceTarget           | Actor  | Vanilla (Common)  | |
| A_Scream               | Actor  | Vanilla (Common)  | |
| A_Pain                 | Actor  | Vanilla (Common)  | |
| A_FreeTargMobj         | Actor  | Vanilla (Common)  | |
| A_RestoreSpecialThing1 | Actor  | Vanilla (Common)  | |
| A_RestoreSpecialThing2 | Actor  | Vanilla (Common)  | |
| A_RestoreArtifact      | Actor  | Vanilla (Common)  | |
| A_HideThing            | Actor  | Vanilla (Common)  | |
| A_UnHideThing          | Actor  | Vanilla (Common)  | |
| A_NoBlocking           | Actor  | Vanilla (Common)  | |
| A_AddPlayerCorpse      | Actor  | Vanilla (Common)  | |
| A_SkullPop             | Actor  | Vanilla (Common)  | |
| A_CheckBurnGone        | Actor  | Vanilla (Common)  | |
| A_CheckSkullFloor      | Actor  | Vanilla (Common)  | |
| A_CheckSkullDone       | Actor  | Vanilla (Common)  | |
| A_ESound               | Actor  | Modified          | Now plays the actor's 'activesound', instead of doing a hardcoded branch based on mobj type. |
| A_PlayerPain           | Actor  | Vanilla (Heretic) | |
| A_PlayerScream         | Actor  | Vanilla (Heretic) | |
| A_BossDeath            | Actor  | Vanilla (Heretic) | |
| A_Explode              | Actor  | Modified          | Branches based on actor's behavior field, instead of its mobjtype |
| A_PodPain              | Actor  | Vanilla (Heretic) | |
| A_RemovePod            | Actor  | Vanilla (Heretic) | |
| A_MakePod              | Actor  | Vanilla (Heretic) | |
| A_InitKeyGizmo         | Actor  | Vanilla (Heretic) | |
| A_VolcanoSet           | Actor  | Vanilla (Heretic) | |
| A_VolcanoBlast         | Actor  | Vanilla (Heretic) | |
| A_BeastPuff            | Actor  | Vanilla (Heretic) | |
| A_VolcBallImpact       | Actor  | Vanilla (Heretic) | |
| A_SpawnTeleGlitter     | Actor  | Vanilla (Heretic) | |
| A_SpawnTeleGlitter2    | Actor  | Vanilla (Heretic) | |
| A_AccTeleGlitter       | Actor  | Vanilla (Heretic) | |
| A_StaffAttackPL1       | Weapon | Vanilla (Heretic) | |
| A_StaffAttackPL2       | Weapon | Vanilla (Heretic) | |
| A_BeakReady            | Weapon | Vanilla (Heretic) | |
| A_BeakRaise            | Weapon | Vanilla (Heretic) | |
| A_BeakAttackPL1        | Weapon | Vanilla (Heretic) | |
| A_BeakAttackPL2        | Weapon | Vanilla (Heretic) | |
| A_GauntletAttack       | Weapon | Vanilla (Heretic) | |
| A_FireBlasterPL1       | Weapon | Vanilla (Heretic) | |
| A_FireBlasterPL2       | Weapon | Vanilla (Heretic) | |
| A_SpawnRippers         | Actor  | Vanilla (Heretic) | |
| A_FireMacePL1          | Weapon | Vanilla (Heretic) | |
| A_FireMacePL2          | Weapon | Vanilla (Heretic) | |
| A_MacePL1Check         | Actor  | Vanilla (Heretic) | |
| A_MaceBallImpact       | Actor  | Vanilla (Heretic) | |
| A_MaceBallImpact2      | Actor  | Vanilla (Heretic) | |
| A_DeathBallImpact      | Actor  | Vanilla (Heretic) | |
| A_FireSkullRodPL1      | Weapon | Vanilla (Heretic) | |
| A_FireSkullRodPL2      | Weapon | Vanilla (Heretic) | |
| A_SkullRodPL2Seek      | Actor  | Vanilla (Heretic) | |
| A_AddPlayerRain        | Actor  | Vanilla (Heretic) | |
| A_HideInCeiling        | Actor  | Vanilla (Heretic) | |
| A_SkullRodStorm        | Actor  | Vanilla (Heretic) | |
| A_RainImpact           | Actor  | Vanilla (Heretic) | |
| A_FireGoldWandPL1      | Weapon | Vanilla (Heretic) | |
| A_FireGoldWandPL2      | Weapon | Vanilla (Heretic) | |
| A_FirePhoenixPL1       | Weapon | Vanilla (Heretic) | |
| A_InitPhoenixPL2       | Weapon | Vanilla (Heretic) | |
| A_FirePhoenixPL2       | Weapon | Vanilla (Heretic) | |
| A_ShutdownPhoenixPL2   | Weapon | Vanilla (Heretic) | |
| A_PhoenixPuff          | Actor  | Vanilla (Heretic) | |
| A_FlameEnd             | Actor  | Vanilla (Heretic) | |
| A_FloatPuff            | Actor  | Vanilla (Heretic) | |
| A_FireCrossbowPL1      | Weapon | Vanilla (Heretic) | |
| A_FireCrossbowPL2      | Weapon | Vanilla (Heretic) | |
| A_BoltSpark            | Actor  | Vanilla (Heretic) | |
| A_FlameSnd             | Actor  | Vanilla (Heretic) | |
| A_Feathers             | Actor  | Vanilla (Heretic) | |
| A_ChicLook             | Actor  | Vanilla (Heretic) | |
| A_ChicChase            | Actor  | Vanilla (Heretic) | |
| A_ChicPain             | Actor  | Vanilla (Heretic) | |
| A_ChicAttack           | Actor  | Vanilla (Heretic) | |
| A_MummyAttack          | Actor  | Vanilla (Heretic) | |
| A_MummyAttack2         | Actor  | Vanilla (Heretic) | |
| A_MummySoul            | Actor  | Vanilla (Heretic) | |
| A_ContMobjSound        | Actor  | Vanilla (Heretic) | |
| A_MummyFX1Seek         | Actor  | Vanilla (Heretic) | |
| A_BeastAttack          | Actor  | Vanilla (Heretic) | |
| A_SnakeAttack          | Actor  | Vanilla (Heretic) | |
| A_SnakeAttack2         | Actor  | Vanilla (Heretic) | |
| A_LichAttack           | Actor  | Vanilla (Heretic) | Named A_HeadAttack in vanilla; renamed to avoid ambiguity with Cacodemon attack function (matches GZDoom's naming) |
| A_HeadIceImpact        | Actor  | Vanilla (Heretic) | |
| A_HeadFireGrow         | Actor  | Vanilla (Heretic) | |
| A_WhirlwindSeek        | Actor  | Vanilla (Heretic) | |
| A_ClinkAttack          | Actor  | Vanilla (Heretic) | |
| A_WizAtk1              | Actor  | Vanilla (Heretic) | |
| A_WizAtk2              | Actor  | Vanilla (Heretic) | |
| A_WizAtk3              | Actor  | Vanilla (Heretic) | |
| A_GhostOff             | Actor  | Vanilla (Heretic) | |
| A_ImpMeAttack          | Actor  | Vanilla (Heretic) | |
| A_ImpMsAttack          | Actor  | Vanilla (Heretic) | |
| A_ImpMsAttack2         | Actor  | Vanilla (Heretic) | |
| A_ImpDeath             | Actor  | Vanilla (Heretic) | |
| A_ImpXDeath1           | Actor  | Vanilla (Heretic) | |
| A_ImpXDeath2           | Actor  | Vanilla (Heretic) | |
| A_ImpExplode           | Actor  | Vanilla (Heretic) | |
| A_KnightAttack         | Actor  | Vanilla (Heretic) | |
| A_DripBlood            | Actor  | Vanilla (Heretic) | |
| A_Sor1Chase            | Actor  | Vanilla (Heretic) | |
| A_Sor1Pain             | Actor  | Vanilla (Heretic) | |
| A_Srcr1Attack          | Actor  | Vanilla (Heretic) | |
| A_SorZap               | Actor  | Vanilla (Heretic) | |
| A_SorcererRise         | Actor  | Vanilla (Heretic) | |
| A_SorRise              | Actor  | Vanilla (Heretic) | |
| A_SorSightSnd          | Actor  | Vanilla (Heretic) | |
| A_Srcr2Decide          | Actor  | Vanilla (Heretic) | |
| A_Srcr2Attack          | Actor  | Vanilla (Heretic) | |
| A_Sor2DthInit          | Actor  | Vanilla (Heretic) | |
| A_SorDSph              | Actor  | Vanilla (Heretic) | |
| A_Sor2DthLoop          | Actor  | Vanilla (Heretic) | |
| A_SorDExp              | Actor  | Vanilla (Heretic) | |
| A_SorDBon              | Actor  | Vanilla (Heretic) | |
| A_BlueSpark            | Actor  | Vanilla (Heretic) | |
| A_GenWizard            | Actor  | Vanilla (Heretic) | |
| A_MinotaurAtk1         | Actor  | Vanilla (Heretic) | |
| A_MinotaurDecide       | Actor  | Vanilla (Heretic) | |
| A_MinotaurAtk2         | Actor  | Vanilla (Heretic) | |
| A_MinotaurAtk3         | Actor  | Vanilla (Heretic) | |
| A_MinotaurCharge       | Actor  | Vanilla (Heretic) | |
| A_MntrFloorFire        | Actor  | Vanilla (Heretic) | |
| A_FlameCheck           | Actor  | Vanilla (Hexen)   | |
| A_Summon               | Actor  | Vanilla (Hexen)   | |
| A_ThrustInitUp         | Actor  | Vanilla (Hexen)   | |
| A_ThrustInitDn         | Actor  | Vanilla (Hexen)   | |
| A_ThrustRaise          | Actor  | Vanilla (Hexen)   | |
| A_ThrustBlock          | Actor  | Vanilla (Hexen)   | |
| A_ThrustImpale         | Actor  | Vanilla (Hexen)   | |
| A_ThrustLower          | Actor  | Vanilla (Hexen)   | |
| A_TeloSpawnC           | Actor  | Vanilla (Hexen)   | |
| A_TeloSpawnB           | Actor  | Vanilla (Hexen)   | |
| A_TeloSpawnA           | Actor  | Vanilla (Hexen)   | |
| A_TeloSpawnD           | Actor  | Vanilla (Hexen)   | |
| A_CheckTeleRing        | Actor  | Vanilla (Hexen)   | |
| A_FogSpawn             | Actor  | Vanilla (Hexen)   | |
| A_FogMove              | Actor  | Vanilla (Hexen)   | |
| A_Quake                | Actor  | Vanilla (Hexen)   | |
| A_PoisonBagInit        | Actor  | Vanilla (Hexen)   | |
| A_PoisonBagDamage      | Actor  | Vanilla (Hexen)   | |
| A_PoisonBagCheck       | Actor  | Vanilla (Hexen)   | |
| A_CheckThrowBomb       | Actor  | Vanilla (Hexen)   | |
| A_NoGravity            | Actor  | Vanilla (Hexen)   | |
| A_PotteryExplode       | Actor  | Vanilla (Hexen)   | |
| A_PotteryChooseBit     | Actor  | Vanilla (Hexen)   | |
| A_PotteryCheck         | Actor  | Vanilla (Hexen)   | |
| A_CorpseBloodDrip      | Actor  | Vanilla (Hexen)   | |
| A_CorpseExplode        | Actor  | Vanilla (Hexen)   | |
| A_LeafSpawn            | Actor  | Vanilla (Hexen)   | |
| A_LeafThrust           | Actor  | Vanilla (Hexen)   | |
| A_LeafCheck            | Actor  | Vanilla (Hexen)   | |
| A_BridgeInit           | Actor  | Vanilla (Hexen)   | |
| A_BridgeOrbit          | Actor  | Vanilla (Hexen)   | |
| A_TreeDeath            | Actor  | Vanilla (Hexen)   | |
| A_PoisonShroom         | Actor  | Vanilla (Hexen)   | |
| A_SoAExplode           | Actor  | Vanilla (Hexen)   | |
| A_BellReset1           | Actor  | Vanilla (Hexen)   | |
| A_BellReset2           | Actor  | Vanilla (Hexen)   | |
| A_FPunchAttack         | Weapon | Vanilla (Hexen)   | |
| A_FAxeCheckUp          | Weapon | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckUpG         | Weapon | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckReady       | Weapon | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckReadyG      | Weapon | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckAtk         | Weapon | New               | Identical to GZDoom's function of the same name. |
| A_FAxeAttack           | Weapon | Vanilla (Hexen)   | |
| A_SnoutAttack          | Weapon | Vanilla (Hexen)   | |
| A_FHammerAttack        | Weapon | Vanilla (Hexen)   | |
| A_FHammerThrow         | Weapon | Vanilla (Hexen)   | |
| A_FSwordAttack         | Weapon | Vanilla (Hexen)   | |
| A_FSwordFlames         | Actor  | Vanilla (Hexen)   | |
| A_CMaceAttack          | Weapon | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_CStaffInitBlink      | Weapon | Vanilla (Hexen)   | |
| A_CStaffCheckBlink     | Weapon | Vanilla (Hexen)   | |
| A_CStaffCheck          | Weapon | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_CStaffAttack         | Weapon | Vanilla (Hexen)   | |
| A_CStaffMissileSlither | Actor  | Vanilla (Hexen)   | |
| A_CFlameAttack         | Weapon | Vanilla (Hexen)   | |
| A_CFlameRotate         | Actor  | Vanilla (Hexen)   | |
| A_CFlamePuff           | Actor  | Vanilla (Hexen)   | |
| A_CFlameMissile        | Actor  | Vanilla (Hexen)   | |
| A_CHolyAttack          | Weapon | Vanilla (Hexen)   | |
| A_CHolyPalette         | Weapon | Vanilla (Hexen)   | |
| A_CHolySeek            | Actor  | Vanilla (Hexen)   | |
| A_CHolyCheckScream     | Actor  | Vanilla (Hexen)   | |
| A_CHolyTail            | Actor  | Vanilla (Hexen)   | |
| A_CHolySpawnPuff       | Actor  | Vanilla (Hexen)   | |
| A_CHolyAttack2         | Actor  | Vanilla (Hexen)   | |
| A_MWandAttack          | Weapon | Vanilla (Hexen)   | |
| A_LightningReady       | Weapon | Vanilla (Hexen)   | |
| A_MLightningAttack     | Weapon | Vanilla (Hexen)   | |
| A_LightningZap         | Actor  | Vanilla (Hexen)   | |
| A_LightningClip        | Actor  | Vanilla (Hexen)   | |
| A_LightningRemove      | Actor  | Vanilla (Hexen)   | |
| A_LastZap              | Actor  | Vanilla (Hexen)   | |
| A_ZapMimic             | Actor  | Vanilla (Hexen)   | |
| A_MStaffAttack         | Weapon | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_MStaffPalette        | Weapon | Vanilla (Hexen)   | |
| A_MStaffWeave          | Actor  | Vanilla (Hexen)   | |
| A_MStaffTrack          | Actor  | Vanilla (Hexen)   | |
| A_SnoutAttack          | Actor  | Vanilla (Hexen)   | |
| A_FireConePL1          | Weapon | Vanilla (Hexen)   | |
| A_ShedShard            | Actor  | Vanilla (Hexen)   | |
| A_FreezeDeath          | Actor  | Vanilla (Hexen)   | |
| A_FreezeDeathChunks    | Actor  | Vanilla (Hexen)   | |
| A_SpeedFade            | Actor  | Vanilla (Hexen)   | |
| A_IceSetTics           | Actor  | Vanilla (Hexen)   | |
| A_IceCheckHeadDone     | Actor  | Vanilla (Hexen)   | |
| A_PigPain              | Actor  | Vanilla (Hexen)   | |
| A_PigLook              | Actor  | Vanilla (Hexen)   | |
| A_PigChase             | Actor  | Vanilla (Hexen)   | |
| A_PigAttack            | Actor  | Vanilla (Hexen)   | |
| A_QueueCorpse          | Actor  | Vanilla (Hexen)   | |
| A_CentaurAttack        | Actor  | Vanilla (Hexen)   | |
| A_CentaurAttack2       | Actor  | Vanilla (Hexen)   | |
| A_SetReflective        | Actor  | Vanilla (Hexen)   | |
| A_CentaurDefend        | Actor  | Vanilla (Hexen)   | |
| A_UnSetReflective      | Actor  | Vanilla (Hexen)   | |
| A_CentaurDropStuff     | Actor  | Vanilla (Hexen)   | |
| A_CheckFloor           | Actor  | Vanilla (Hexen)   | |
| A_DemonAttack1         | Actor  | Vanilla (Hexen)   | |
| A_DemonAttack2         | Actor  | Vanilla (Hexen)   | |
| A_DemonDeath           | Actor  | Vanilla (Hexen)   | |
| A_Demon2Death          | Actor  | Vanilla (Hexen)   | |
| A_WraithRaiseInit      | Actor  | Vanilla (Hexen)   | |
| A_WraithRaise          | Actor  | Vanilla (Hexen)   | |
| A_WraithInit           | Actor  | Vanilla (Hexen)   | |
| A_WraithLook           | Actor  | Vanilla (Hexen)   | |
| A_WraithChase          | Actor  | Vanilla (Hexen)   | |
| A_WraithFX3            | Actor  | Vanilla (Hexen)   | |
| A_WraithMelee          | Actor  | Vanilla (Hexen)   | |
| A_WraithMissile        | Actor  | Vanilla (Hexen)   | |
| A_WraithFX2            | Actor  | Vanilla (Hexen)   | |
| A_MinotaurFade1        | Actor  | Vanilla (Hexen)   | |
| A_MinotaurFade2        | Actor  | Vanilla (Hexen)   | |
| A_MinotaurLook         | Actor  | Vanilla (Hexen)   | |
| A_MinotaurChase        | Actor  | Vanilla (Hexen)   | |
| A_MinotaurRoam         | Actor  | Vanilla (Hexen)   | |
| A_MinotaurAtk1         | Actor  | Vanilla (Hexen)   | |
| A_MinotaurDecide       | Actor  | Vanilla (Hexen)   | |
| A_MinotaurAtk2         | Actor  | Vanilla (Hexen)   | |
| A_MinotaurAtk3         | Actor  | Vanilla (Hexen)   | |
| A_MinotaurCharge       | Actor  | Vanilla (Hexen)   | |
| A_SmokePuffExit        | Actor  | Vanilla (Hexen)   | |
| A_MinotaurFade0        | Actor  | Vanilla (Hexen)   | |
| A_MntrFloorFire        | Actor  | Vanilla (Hexen)   | |
| A_SerpentChase         | Actor  | Vanilla (Hexen)   | |
| A_SerpentHumpDecide    | Actor  | Vanilla (Hexen)   | |
| A_SerpentUnHide        | Actor  | Vanilla (Hexen)   | |
| A_SerpentRaiseHump     | Actor  | Vanilla (Hexen)   | |
| A_SerpentLowerHump     | Actor  | Vanilla (Hexen)   | |
| A_SerpentHide          | Actor  | Vanilla (Hexen)   | |
| A_SerpentBirthScream   | Actor  | Vanilla (Hexen)   | |
| A_SetShootable         | Actor  | Vanilla (Hexen)   | |
| A_SerpentCheckForAttack| Actor  | Vanilla (Hexen)   | |
| A_UnSetShootable       | Actor  | Vanilla (Hexen)   | |
| A_SerpentDiveSound     | Actor  | Vanilla (Hexen)   | |
| A_SerpentWalk          | Actor  | Vanilla (Hexen)   | |
| A_SerpentChooseAttack  | Actor  | Vanilla (Hexen)   | |
| A_SerpentMeleeAttack   | Actor  | Vanilla (Hexen)   | |
| A_SerpentMissileAttack | Actor  | Vanilla (Hexen)   | |
| A_SerpentHeadPop       | Actor  | Vanilla (Hexen)   | |
| A_SerpentSpawnGibs     | Actor  | Vanilla (Hexen)   | |
| A_SerpentHeadCheck     | Actor  | Vanilla (Hexen)   | |
| A_FloatGib             | Actor  | Vanilla (Hexen)   | |
| A_DelayGib             | Actor  | Vanilla (Hexen)   | |
| A_SinkGib              | Actor  | Vanilla (Hexen)   | |
| A_BishopDecide         | Actor  | Vanilla (Hexen)   | |
| A_BishopDoBlur         | Actor  | Vanilla (Hexen)   | |
| A_BishopSpawnBlur      | Actor  | Vanilla (Hexen)   | |
| A_BishopChase          | Actor  | Vanilla (Hexen)   | |
| A_BishopAttack         | Actor  | Vanilla (Hexen)   | |
| A_BishopAttack2        | Actor  | Vanilla (Hexen)   | |
| A_BishopPainBlur       | Actor  | Vanilla (Hexen)   | |
| A_BishopPuff           | Actor  | Vanilla (Hexen)   | |
| A_SetAltShadow         | Actor  | Vanilla (Hexen)   | |
| A_BishopMissileWeave   | Actor  | Vanilla (Hexen)   | |
| A_BishopMissileSeek    | Actor  | Vanilla (Hexen)   | |
| A_DragonInitFlight     | Actor  | Vanilla (Hexen)   | |
| A_DragonFlap           | Actor  | Vanilla (Hexen)   | |
| A_DragonFlight         | Actor  | Vanilla (Hexen)   | |
| A_DragonAttack         | Actor  | Vanilla (Hexen)   | |
| A_DragonPain           | Actor  | Vanilla (Hexen)   | |
| A_DragonCheckCrash     | Actor  | Vanilla (Hexen)   | |
| A_DragonFX2            | Actor  | Vanilla (Hexen)   | |
| A_EttinAttack          | Actor  | Vanilla (Hexen)   | |
| A_DropMace             | Actor  | Vanilla (Hexen)   | |
| A_FiredRocks           | Actor  | Vanilla (Hexen)   | |
| A_UnSetInvulnerable    | Actor  | Vanilla (Hexen)   | |
| A_FiredChase           | Actor  | Vanilla (Hexen)   | |
| A_FiredAttack          | Actor  | Vanilla (Hexen)   | |
| A_FiredSplotch         | Actor  | Vanilla (Hexen)   | |
| A_SmBounce             | Actor  | Vanilla (Hexen)   | |
| A_IceGuyLook           | Actor  | Vanilla (Hexen)   | |
| A_IceGuyChase          | Actor  | Vanilla (Hexen)   | |
| A_IceGuyAttack         | Actor  | Vanilla (Hexen)   | |
| A_IceGuyDie            | Actor  | Vanilla (Hexen)   | |
| A_IceGuyMissilePuff    | Actor  | Vanilla (Hexen)   | |
| A_IceGuyMissileExplode | Actor  | Vanilla (Hexen)   | |
| A_ClassBossHealth      | Actor  | Vanilla (Hexen)   | |
| A_FastChase            | Actor  | Vanilla (Hexen)   | |
| A_FighterAttack        | Actor  | Vanilla (Hexen)   | |
| A_ClericAttack         | Actor  | Vanilla (Hexen)   | |
| A_MageAttack           | Actor  | Vanilla (Hexen)   | |
| A_SorcSpinBalls        | Actor  | Vanilla (Hexen)   | |
| A_SpeedBalls           | Actor  | Vanilla (Hexen)   | |
| A_SpawnFizzle          | Actor  | Vanilla (Hexen)   | |
| A_SorcBossAttack       | Actor  | Vanilla (Hexen)   | |
| A_SorcBallOrbit        | Actor  | Vanilla (Hexen)   | |
| A_SorcBallPop          | Actor  | Vanilla (Hexen)   | |
| A_BounceCheck          | Actor  | Vanilla (Hexen)   | |
| A_SorcFX1Seek          | Actor  | Vanilla (Hexen)   | |
| A_SorcFX2Split         | Actor  | Vanilla (Hexen)   | |
| A_SorcFX2Orbit         | Actor  | Vanilla (Hexen)   | |
| A_SorcererBishopEntry  | Actor  | Vanilla (Hexen)   | |
| A_SpawnBishop          | Actor  | Vanilla (Hexen)   | |
| A_SorcFX4Check         | Actor  | Vanilla (Hexen)   | |
| A_KoraxStep2           | Actor  | Vanilla (Hexen)   | |
| A_KoraxChase           | Actor  | Vanilla (Hexen)   | |
| A_KoraxStep            | Actor  | Vanilla (Hexen)   | |
| A_KoraxDecide          | Actor  | Vanilla (Hexen)   | |
| A_KoraxMissile         | Actor  | Vanilla (Hexen)   | |
| A_KoraxCommand         | Actor  | Vanilla (Hexen)   | |
| A_KoraxBonePop         | Actor  | Vanilla (Hexen)   | |
| A_KSpiritRoam          | Actor  | Vanilla (Hexen)   | |
| A_KBoltRaise           | Actor  | Vanilla (Hexen)   | |
| A_KBolt                | Actor  | Vanilla (Hexen)   | |
| A_BatSpawnInit         | Actor  | Vanilla (Hexen)   | |
| A_BatSpawn             | Actor  | Vanilla (Hexen)   | |
| A_BatMove              | Actor  | Vanilla (Hexen)   | |
| A_FlyTarget            | Actor  | New               | Likely identical to GZDoom's A_FlySearch; name taken from original source code |
| A_FlyChase             | Actor  | New               | Likely identical to GZDoom's A_FlyBuzz; name taken from original source code |

## Codepointer Overview EX

| Name                     | Type      | Origin | Description |
|--------------------------|-----------|--------|-------------|
| A_PlaySound              | Actor     |        | |
| A_FaceTargetSound        | Actor     |        | |
| A_RandomJump             | Actor     |        | |
| A_SpawnObject            | Actor     |        | |
| A_MonsterProjectile      | Actor     |        | |
| A_MonsterBulletAttack    | Actor     |        | |
| A_MonsterMeleeAttack     | Actor     |        | |
| A_MonsterComboAttack     | Actor     |        | |
| A_SpawnMinion            | Actor     |        | |
| A_RadiusDamage           | Actor     |        | |
| A_SeekTracer             | Actor     |        | |
| A_FindTracer             | Actor     |        | |
| A_ClearTracer            | Actor     |        | |
| A_MissileExplode         | Actor     |        | |
| A_MissileCountdown       | Actor     |        | |
| A_WeaponSound            | Weapon    |        | |
| A_WeaponProjectile       | Weapon    |        | |
| A_WeaponBulletAttack     | Weapon    |        | |
| A_WeaponMeleeAttack      | Weapon    |        | |
| A_ConsumeAmmo            | Weapon    |        | |
| A_GauntletAttackEX       | Weapon    |        | |
| A_FireGoldWandEX1        | Weapon    |        | |
| A_FireCrossbowEX1        | Weapon    |        | |
| A_FireCrossbowEX2        | Weapon    |        | |
| A_FireSkullRodEX1        | Weapon    |        | |
| A_FireSkullRodEX2        | Weapon    |        | |
| A_FirePhoenixEX2         | Weapon    |        | |
| A_FireMaceEX1            | Weapon    |        | |
| A_FireMaceEX2            | Weapon    |        | |
| A_DeathBallBounceEX      | Actor     |        | |
| A_FireTempestWandPL1     | Weapon    |        | |
| A_TempestChain           | Actor     |        | |
| A_TempestSpray           | Actor     |        | |
| A_KnightAttackEX         | Actor     |        | |
| A_ClinkRushEX            | Actor     |        | |
| A_ClinkAttackEX          | Actor     |        | |
| A_MinotaurMeleeEX        | Actor     |        | |
| A_TrollChargeStart       | Actor     |        | |
| A_TrollCharge            | Actor     |        | |
| A_SuperDemonAttack1      | Actor     |        | |
| A_SuperDemonAttack2      | Actor     |        | |
| A_CShieldParry           | Weapon    |        | |
| A_CShieldGuard           | Weapon    |        | |
| A_CShieldLower           | Weapon    |        | |
| A_FireConeEX1            | Weapon    |        | |
| A_ShedShardEX            | Actor     |        | |
| A_MStaffAttackEX         | Weapon    |        | |
| A_CentaurEXGuardRaise    | Actor     |        | |
| A_CentaurEXGuardHold     | Actor     |        | |
| A_CentaurEXGuardLower    | Actor     |        | |
| A_CentaurAttack2EX       | Actor     |        | |
| A_InvulnStandardStart    | Player    |        | |
| A_InvulnStandardEnd      | Player    |        | |
| A_InvulnPhasedTick       | Player    |        | |
| A_InvulnPhasedDraw       | PlayerVis |        | |
| A_InvulnPhasedEnd        | Player    |        | |
| A_InvulnReflectiveStart  | Player    |        | |
| A_InvulnReflectiveEnd    | Player    |        | |
| A_PickupHealth           | Pickup    |        | |
| A_PickupBasicArmor       | Pickup    |        | |
| A_PickupHexenArmor       | Pickup    |        | |
| A_PickupBackpack         | Pickup    |        | |
| A_PickupWeapon           | Pickup    |        | |
| A_PickupWeaponPiece      | Pickup    |        | |
| A_PickupAmmo             | Pickup    |        | |
| A_PickupDualAmmo         | Pickup    |        | |
| A_PickupPower            | Pickup    |        | |
| A_PickupArtifact         | Pickup    |        | |
| A_PickupKey              | Pickup    |        | |
| A_PowerInvulnerableStart | Power     |        | |
| A_PowerInvulnerableTick  | Power     |        | |
| A_PowerInvulnerableEnd   | Power     |        | |
| A_PowerInvisibilityStart | Power     |        | |
| A_PowerInvisibilityEnd   | Power     |        | |
| A_PowerStrengthStart     | Power     |        | |
| A_PowerFlightStart       | Power     |        | |
| A_PowerFlightEnd         | Power     |        | |
| A_PowerWeaponLevel2Start | Power     |        | |
| A_PowerWeaponLevel2End   | Power     |        | |
| A_ArtiGivePower          | Artifact  |        | |
| A_ArtiHealth             | Artifact  |        | |
| A_ArtiEgg                | Artifact  |        | |
| A_ArtiSummon             | Artifact  |        | |
| A_ArtiTeleport           | Artifact  |        | |
| A_ArtiTeleportOther      | Artifact  |        | |
| A_ArtiBoostMana          | Artifact  |        | |
| A_ArtiBoostArmor         | Artifact  |        | |
| A_ArtiBlastRadius        | Artifact  |        | |
| A_ArtiFlechette          | Artifact  |        | |
| A_ArtiThrowingBomb       | Artifact  |        | |
| A_ArtiThrowingBombEX     | Artifact  |        | |
| A_ArtiPoisonBag          | Artifact  |        | |
| A_ArtiFireBomb           | Artifact  |        | |
| A_ArtiHealingRadius      | Artifact  |        | |
| A_RadiusGiveArmor        | Artifact  |        | |
| A_RadiusGiveArmorEX      | Artifact  |        | |
| A_RadiusGiveHealth       | Artifact  |        | |
| A_RadiusGiveMana         | Artifact  |        | |
| A_RadiusGiveManaEX       | Artifact  |        | |
| A_ArtiPuzzleItem         | Artifact  |        | |
