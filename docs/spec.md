# Raven EX Specification

`TODO-SUMMARY`

`TODO-TOC`

[XA] Some rough notes-to-self on Hexen things to prioritize (in no particular order):
- demon_green `behavior`
- describe custom states
- bishop `blur` custom state & de-hardcode
- fighter punch `fire2` custom state & de-hardcode
- fighter axe `A_FAxeCheckAtk` de-hardcode (and all the new axe pointers in general tbh)
- playerclass flechette action
- playerclass healingradius action
- weapon AUTOAIMFARTHEST flag
- weapon INTERPOLATE flag
- state NOINTERPOLATE flag
- puff definitions (or at least the `hitsound` property)

## Game Mechanics

### Melee Targeting

In Heretic, player melee attacks search for a valid target directly in front of a player, out to a specified range.

In Hexen, player melee attacks search for a valid target within a 90-degree field-of-view, out to the specified range.

In either case, if a valid target is found, the player's view snaps directly to face the target.

Rather than branch by game, the melee targeting method is tied to the action function itself, e.g. [A_GauntletAttackEX](#A_GauntletAttackEX) uses Heretic-style targeting, while [A_CMaceAttackEX](#A_CMaceAttackEX) uses Hexen-style targeting.

### Shield Block & Parry

The cleric now wields a shield alongside his trusty Mace of Contrition. The shield allows for attacks to be blocked, reducing damage. With good timing, you can even reflect projectiles and parry enemy melee attacks, stunning them for a few tics and leaving them wide open for a Mace counterattack.

Centaurs also utilize the new block mechanics: their shield now only protects them from the front, allowing players to circle around them and strike from behind.

This feature has several moving parts. Implementation details don't have to match exactly as long as the user-facing API (i.e. the actions) remains the same. The exact methodology used in Heretic + Hexen is shared here for clarity.

Components:
- Weapon features:
  - [Altfire](#Altfire)
- Weapon actions:
  - [A_CShieldParry](#A_CShieldParry)
  - [A_CShieldGuard](#A_CShieldGuard)
  - [A_CShieldLower](#A_CShieldLower)
- Actor actions:
  - [A_CentaurEXGuardRaise](#A_CentaurEXGuardRaise)
  - [A_CentaurEXGuardHold](#A_CentaurEXGuardHold)
  - [A_CentaurEXGuardLower](#A_CentaurEXGuardLower)
- Internal actor flags:
  - [SHIELDPARRY](#MF_SHIELDPARRY)
  - [STUNNED](#MF_STUNNED)
- Public actor properties:
  - [stunstate](#stunstate)
- Internal actor variables:
  - [guardangle](#guardangle)
  - [guardpercent](#guardpercent)
- Internal player variables:
  - [lastguardtime](#lastguardtime)

#### Altfire

`TODO-SUMMARY -- it's altfire; straightforward, conceptually. still worth explaining some of the weird caveats in H+H's implementation though`

#### SHIELDPARRY flag<a id="MF_SHIELDPARRY" name="MF_SHIELDPARRY"></a>

An actor with the `SHIELDPARRY` flag is considered to be parrying. This has a variety of effects, outlined below.

If a parrying actor is struck by a melee attack, the attacker is punished as follows:
- If attacker is a player, set `reactiontime` to `18` (i.e. block movement for 18 tics)
  - This value will ideally be de-hardcoded in a future spec
- Else if attacker's [`stunstate`](#stunstate) is not null:
  - Set the [`STUNNED`](#MF_STUNNED) flag
  - Jump to [`stunstate`](#stunstate)
- Else attacker jumps to `painstate`

If a parrying actor is struck by a missile (i.e. an actor with the `MISSILE` flag), the missile is reflected back to its owner as follows:
- If missile's owner is neither a player nor a Hexen class boss (via `behavior` field check), multiply missile's `damage` by `5`
  - This value will ideally be de-hardcoded in a future spec
- Reverse the missile's velocity (i.e. send it back the way it came)
- If missile has the `SEEKERMISSILE` flag set, set its `tracer` to its current `target` (owner)
- Set the missile's `target` (owner) to the parrying actor

If the attack foils invulnerability (i.e. damage is over 10k or the inflictor has the [`FOILINVUL`](#MF_FOILINVUL) flag), then the attack cannot be parried.

Non-melee hitscan attacks cannot be parried.

When successfully parrying either a melee attacker or a missile, if parrying actor is a player then set their [`lastguardtime`](#lastguardtime) field to the current `leveltime`. This is used by the Cleric shield weapon codepointers to animate the shield sprite "recoiling" when struck.

If a parrying actor is struck by a hitscan attack (melee or ranged), the puff spawning function will behave as if the actor has the `NOBLOOD` flag (e.g. spawn a puff instead of a bloodsplat).

The `SHIELDPARRY` flag is only ever set by the [A_CShieldParry](#A_CShieldParry) weapon function.

The `SHIELDPARRY` flag is cleared if any of the following occurs:
- The actor dies
- The actor is a player and their weapon calls either [A_CShieldGuard](#A_CShieldGuard) or [A_CShieldLower](#A_CShieldLower)

#### STUNNED flag<a id="MF_STUNNED" name="MF_STUNNED"></a>

Actors with the `STUNNED` flag take extra damage from [A_CMaceAttackEX](#A_CMaceAttackEX). This is the flag's only effect; all other stun-related behavior is handled via [`stunstate`](#stunstate).

The `STUNNED` flag is only ever set on an actor when melee attackign an actor with the [`SHIELDPARRY`](#MF_SHIELDPARRY) flag.

The `STUNNED` flag is cleared if any of the following occurs:
- The actor calls A_Chase
- The actor is put into its `painstate`

#### stunstate

When an actor is [stunned](#MF_STUNNED), it will jump to its `stunstate` if not null.

Heretic + Hexen's gameplay enhancements use this to 'freeze' the actor in place for a few seconds by defining a few long-duration states, but this is simply a convention. The behavior can ultimately be customized via EXDEFS to do something different if desired.

#### guardangle & guardpercent<a id="guardangle" name="guardangle"></a><a id="guardpercent" name="guardpercent"></a>

Actors (mobjs) have two new internal fields: `guardangle` and `guardpercent`.

The `guardangle` field represents a cone, centered at the guarding actor's facing angle. If the field is non-zero and the vector from the guarding actor to the attacking actor falls within the cone, then the attack is guarded.

The `guardangle` field represents the whole arc, i.e. a value of 90 degrees will guard attacks from -45 to 45 degrees relative to the calling actor's facing angle.

Guarded attacks will have their damage _reduced_ by a factor of `guardpercent`, e.g. a value of `0.25` will result in the player taking 75% of the attack's full damage value. A value of `1.0` will completely negate all damage from the attack.

If the attack foils invulnerability (i.e. damage is over 10k or inflictor has the [`FOILINVUL`](#MF_FOILINVUL) flag), then the attack cannot be guarded.

Damaging floors, crushers, poison clouds, and instant-kills cannot be guarded.

When successfully guarding an attack, if guarding actor is a player then set their `lastguardtime` field to the current `leveltime`. This is used by the Cleric shield weapon codepointers to animate the shield visually "recoiling" when struck.

Both `guardangle` and `guardpercent` are set to zero if any of the following occurs:
- The actor dies
- The actor calls [A_CentaurEXGuardLower](#A_CentaurEXGuardLower)
- The actor is a player and their weapon calls [A_CShieldLower](#A_CShieldLower)

#### lastguardtime & Shield Block Offset<a id="lastguardtime" name="lastguardtime"></a><a id="shield-block-offset" name="shield-block-offset"></a>

When a player parryies or guards an attack, the player's `lastguardtime` variable is set to the current `leveltime`. This value is then used to vertically offset the weapon sprite to make it visibly "recoil" when struck.

Both the [A_CShieldParry](#A_CShieldParry) and [A_CShieldBlock](#A_CShieldBlock) functions will update the weapon psprite's Y offset (the `sy` field) according to the following formula:
```
psp->sy = WEAPONTOP + (1 << max(0, offsettics - leveltime - lastguardtime + 1))
```

...where `offsettics` is `4` when updated from [A_CShieldParry](#A_CShieldParry) and `3` when updated from [A_CShieldBlock](#A_CShieldBlock).

In practice, this results in a stronger 'recoil' effect on the weapon sprite when parrying an attack versus blocking it.

## Actors

Also known as 'things' in DEHACKED, 'mobjs' in the source code, and 'entities' in virtually every other game engine.

### Properties

| Name              | Type        | Description |
|-------------------|-------------|-------------|
| doomednum         | integer     | `TODO-PROP-DESC` |
| spawnid           | integer     | `TODO-PROP-DESC` |
| spawnstate        | state       | `TODO-PROP-DESC` |
| seestate          | state       | `TODO-PROP-DESC` |
| painstate         | state       | `TODO-PROP-DESC` |
| meleestate        | state       | `TODO-PROP-DESC` |
| missilestate      | state       | `TODO-PROP-DESC` |
| crashstate        | state       | `TODO-PROP-DESC` |
| deathstate        | state       | `TODO-PROP-DESC` |
| xdeathstate       | state       | `TODO-PROP-DESC` |
| icedeathstate     | state       | `TODO-PROP-DESC` |
| activatestate     | state       | `TODO-PROP-DESC` |
| deactivatestate   | state       | `TODO-PROP-DESC` |
| raisestate        | state       | `TODO-PROP-DESC` |
| stunstate         | state       | `TODO-PROP-DESC` |
| customstates      | object      | `TODO-PROP-DESC` |
| seesound          | sound       | `TODO-PROP-DESC` |
| activesound       | sound       | `TODO-PROP-DESC` |
| attacksound       | sound       | `TODO-PROP-DESC` |
| ripsound          | sound       | `TODO-PROP-DESC` |
| painsound         | sound       | `TODO-PROP-DESC` |
| deathsound        | sound       | `TODO-PROP-DESC` |
| activatesound     | sound       | `TODO-PROP-DESC` |
| deactivatesound   | sound       | `TODO-PROP-DESC` |
| flags             | actor flags | `TODO-PROP-DESC` |
| addflags          | actor flags | `TODO-PROP-DESC` |
| removeflags       | actor flags | `TODO-PROP-DESC` |
| health            | integer     | `TODO-PROP-DESC` |
| reactiontime      | integer     | `TODO-PROP-DESC` |
| painchance        | integer     | `TODO-PROP-DESC` |
| speed             | decimal     | `TODO-PROP-DESC` |
| radius            | decimal     | `TODO-PROP-DESC` |
| height            | decimal     | `TODO-PROP-DESC` |
| mass              | integer     | `TODO-PROP-DESC` |
| damage            | integer     | `TODO-PROP-DESC` |
| damagedice        | integer     | `TODO-PROP-DESC` |
| fastspeed         | decimal     | `TODO-PROP-DESC` |
| meleerange        | decimal     | `TODO-PROP-DESC` |
| infightinggroup   | integer     | `TODO-PROP-DESC` |
| projectilegroup   | integer     | `TODO-PROP-DESC` |
| splashgroup       | integer     | `TODO-PROP-DESC` |
| minrespawntics    | integer     | `TODO-PROP-DESC` |
| minrespawnseconds | decimal     | `TODO-PROP-DESC` |
| respawndice       | integer     | `TODO-PROP-DESC` |
| pickuptype        | pickup      | `TODO-PROP-DESC` |
| translationlump   | string      | `TODO-PROP-DESC` |
| selfdamagefactor  | decimal     | `TODO-PROP-DESC` |
| behavior          | behavior    | mobj behavior; see "Behaviors" tab for valid values & what they do |
| dropitem          | dropitem    | `TODO-PROP-DESC` |
| dropitem2         | dropitem    | `TODO-PROP-DESC` |
| automapoffsetx    | integer     | `TODO-PROP-DESC` |
| automapoffsety    | integer     | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name                | Origin   | Description |
|---------------------|----------|-------------|
| SPECIAL             | Vanilla  | `TODO-FLAG-DESC` |
| SOLID               | Vanilla  | `TODO-FLAG-DESC` |
| SHOOTABLE           | Vanilla  | `TODO-FLAG-DESC` |
| NOSECTOR            | Vanilla  | `TODO-FLAG-DESC` |
| NOBLOCKMAP          | Vanilla  | `TODO-FLAG-DESC` |
| AMBUSH              | Vanilla  | `TODO-FLAG-DESC` |
| JUSTHIT             | Vanilla  | `TODO-FLAG-DESC` |
| JUSTATTACKED        | Vanilla  | `TODO-FLAG-DESC` |
| SPAWNCEILING        | Vanilla  | `TODO-FLAG-DESC` |
| NOGRAVITY           | Vanilla  | `TODO-FLAG-DESC` |
| DROPOFF             | Vanilla  | `TODO-FLAG-DESC` |
| PICKUP              | Vanilla  | `TODO-FLAG-DESC` |
| NOCLIP              | Vanilla  | `TODO-FLAG-DESC` |
| FLOAT               | Vanilla  | `TODO-FLAG-DESC` |
| TELEPORT            | Vanilla  | `TODO-FLAG-DESC` |
| MISSILE             | Vanilla  | `TODO-FLAG-DESC` |
| DROPPED             | Vanilla  | `TODO-FLAG-DESC` |
| SHADOW              | Vanilla  | Actor is translucent _and_ a ghost. Effectively a combo of ZDoom's SHADOW flag, GHOST flag, and Renderstyle "Translucent" using the main translucency percent values (i.e. in ZDoom, HR_SHADOW and HX_SHADOW) |
| NOBLOOD             | Vanilla  | `TODO-FLAG-DESC` |
| CORPSE              | Vanilla  | `TODO-FLAG-DESC` |
| INFLOAT             | Vanilla  | `TODO-FLAG-DESC` |
| COUNTKILL           | Vanilla  | `TODO-FLAG-DESC` |
| COUNTITEM           | Vanilla  | `TODO-FLAG-DESC` |
| SKULLFLY            | Vanilla  | `TODO-FLAG-DESC` |
| NOTDMATCH           | Vanilla  | `TODO-FLAG-DESC` |
| TRANSLATION         | Reserved | N/A -- reserved, but unimplemented |
| TRANSLATION2        | Reserved | N/A -- reserved, but unimplemented |
| TOUCHY              | MBF      | Actor dies when touched; same semantics as ZDoom |
| BOUNCES             | Reserved | N/A -- reserved, but unimplemented |
| FRIEND              | Reserved | N/A -- reserved, but unimplemented |
| TRANSLUCENT         | Boom     | Actor is translucent, using the main translucency percent values (i.e. in ZDoom, HR_SHADOW and HX_SHADOW) -- this flag does not imply projectile inaccuracy or ghost behavior. |
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
| SLIDE               | Vanilla  | slides against walls (e.g. ZDoom's SLIDESONWALLS flag) |
| ONMOBJ              | Vanilla  | mobj is resting on top of another mobj |
| PASSMOBJ            | Vanilla  | Enable z block checking.  If on, this flag will allow the mobj to pass over/under other mobjs. |
| CANNOTPUSH          | Vanilla  | cannot push other pushable mobjs |
| SLIDEONACTORS       | New      | slides against other actors. in vanilla, Hexen actors have both SLIDE and SLIDEONACTORS, while Heretic just has SLIDE -- does ZDoom disambiguate? |
| BOSS                | Vanilla  | mobj is a major boss (lots of internal checks on this for various shenanigans; should be same as ZDoom) |
| FIREDAMAGE          | Vanilla  | does fire damage |
| NODMGTHRUST         | Vanilla  | does not thrust target when damaging |
| TELESTOMP           | Vanilla  | mobj can stomp another |
| FLOATBOB            | Vanilla  | use float bobbing z movement |
| DONTDRAW            | Vanilla  | don't generate a vissprite |
| IMPACT              | Vanilla  | an MF_MISSILE mobj can activate SPAC_IMPACT |
| PUSHWALL            | Vanilla  | mobj can push walls |
| MCROSS              | Vanilla  | can activate monster cross lines; same as ZDoom's "ACTIVATEMCROSS" |
| PCROSS              | Vanilla  | can activate projectile cross lines; same as ZDoom's "ACTIVATEPCROSS" |
| CANTLEAVEFLOORPIC   | Vanilla  | stay within a certain floor type |
| NONSHOOTABLE        | Vanilla  | mobj is totally non-shootable, but still considered solid |
| INVULNERABLE        | Vanilla  | mobj is invulnerable |
| DORMANT             | Vanilla  | thing is dormant |
| ICEDAMAGE           | Vanilla  | does ice damage |
| SEEKERMISSILE       | Modified | is a seeking missile -- in H+H, internally this flag is only checked when reflecting/parrying a missile (i.e. to set its tracer to its target) |
| REFLECTIVE          | Vanilla  | reflects missiles |
| ALTSHADOW           | Vanilla  | Actor is translucent, using the alternative translucency percent values (i.e. in ZDoom, HX_ALTSHADOW) -- this flag does not imply projectile inaccuracy or ghost behavior. |
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
| DRAWONAUTOMAP       | New      | actor's sprite is drawn on the automap -- drawing works just like AutomapMarker in ZDoom, effectively |
| TOGGLEDORMANT       | New      | actor's MF2_DORMANT flag will be toggled when calling Thing Activate/Deactivate on it; previously hardcoded to MF_COUNTKILL -- ZDoom may have different semantics here? |
| PSETRANS            | New      | draw translucent if the "PSE translucency" menu option is enabled -- this may be N/A for ports unless they want to also add this feature to the menu (it's a quick thing to wire up). |
| USESPECIAL          | New      | actor will execute its special when the player presses the Use button while facing it. |
| THRUICECORPSE       | New      | missile will pass through ice corpses |
| SHIELDPARRY         | Internal | Not directly exposed to EXDEFS -- Cleric shield parry: actors with a valid stunstate that perform a melee attack against an actor with SHIELDPARRY will jump to their stunstate and have the STUNNED flag set. |
| STUNNED             | Internal | Not directly exposed to EXDEFS -- Actor is stunned, and takes extra damage from certain melee attacks (e.g. mace). This flag is cleared whenever A_Chase is called or the actor goes into its pain state. |
| NOSRCDMGTHRUST      | New      | Do not thrust when damage is dealt by an attack whose source (not inflictor) has this flag; this is set on Hexen playerclasses, to prevent damage-based thrust for all attacks. |
| FOILINVUL           | New      | missiles with this flag will ignore invulnerability on monsters (but not players) |
| DONTBLAST           | New      | cannot be blasted by the disc of repulsion |
| STAYMORPHED         | New      | monsters with this flag will never unmorph |
| NONETSYNC           | New      | Object is never synchronized on the network (i.e. clientside-only) |
| RANDOMACTIVESOUND   | New      | actor will randomly play its seesound instead of its activesound, 50% of the time (previously hardcoded to Wizard/Bishop/Pig) |
| NOFLYOVER           | New      | actors with this flag will not attempt to fly over each other even if the PASSMOBJ flag is set (previously hardcoded to Gargoyle/Wizard) |

### Actions - Base

Actions (a.k.a. 'codepointers') from the base vanilla games. Unless otherwise mentioned, these functions behave identically to their vanilla counterparts.

| Name                       | Origin             | Notes |
|----------------------------|--------------------|-------|
| A_Look                     | Common             | N/A |
| A_Chase                    | Common             | N/A |
| A_FaceTarget               | Common             | N/A |
| A_Scream                   | Common             | N/A |
| A_Pain                     | Common             | N/A |
| A_FreeTargMobj             | Common             | N/A |
| A_RestoreSpecialThing1     | Common             | N/A |
| A_RestoreSpecialThing2     | Common             | N/A |
| A_RestoreArtifact          | Common             | N/A |
| A_HideThing                | Common             | N/A |
| A_UnHideThing              | Common             | N/A |
| A_NoBlocking               | Common             | N/A |
| A_AddPlayerCorpse          | Common             | N/A |
| A_SkullPop                 | Common             | N/A |
| A_CheckBurnGone            | Common             | N/A |
| A_CheckSkullFloor          | Common             | N/A |
| A_CheckSkullDone           | Common             | N/A |
| [A_ESound](#A_ESound)      | Common - Modified  | Now plays the actor's 'activesound', instead of doing a hardcoded branch based on mobj type. |
| A_PlayerPain               | Heretic            | N/A |
| A_PlayerScream             | Heretic            | N/A |
| A_BossDeath                | Heretic            | N/A |
| [A_Explode](#A_Explode)    | Heretic - Modified | Branches based on actor's behavior field, instead of its mobjtype |
| A_PodPain                  | Heretic            | N/A |
| A_RemovePod                | Heretic            | N/A |
| A_MakePod                  | Heretic            | N/A |
| A_InitKeyGizmo             | Heretic            | N/A |
| A_VolcanoSet               | Heretic            | N/A |
| A_VolcanoBlast             | Heretic            | N/A |
| A_BeastPuff                | Heretic            | N/A |
| A_VolcBallImpact           | Heretic            | N/A |
| A_SpawnTeleGlitter         | Heretic            | N/A |
| A_SpawnTeleGlitter2        | Heretic            | N/A |
| A_AccTeleGlitter           | Heretic            | N/A |
| A_SpawnRippers             | Heretic            | N/A |
| A_MacePL1Check             | Heretic            | N/A |
| A_MaceBallImpact           | Heretic            | N/A |
| A_MaceBallImpact2          | Heretic            | N/A |
| A_DeathBallImpact          | Heretic            | N/A |
| A_SkullRodPL2Seek          | Heretic            | N/A |
| A_AddPlayerRain            | Heretic            | N/A |
| A_HideInCeiling            | Heretic            | N/A |
| A_SkullRodStorm            | Heretic            | N/A |
| A_RainImpact               | Heretic            | N/A |
| A_PhoenixPuff              | Heretic            | N/A |
| A_FlameEnd                 | Heretic            | N/A |
| A_FloatPuff                | Heretic            | N/A |
| A_BoltSpark                | Heretic            | N/A |
| A_FlameSnd                 | Heretic            | N/A |
| A_Feathers                 | Heretic            | N/A |
| A_ChicLook                 | Heretic            | N/A |
| A_ChicChase                | Heretic            | N/A |
| A_ChicPain                 | Heretic            | N/A |
| A_ChicAttack               | Heretic            | N/A |
| A_MummyAttack              | Heretic            | N/A |
| A_MummyAttack2             | Heretic            | N/A |
| A_MummySoul                | Heretic            | N/A |
| A_ContMobjSound            | Heretic            | N/A |
| A_MummyFX1Seek             | Heretic            | N/A |
| A_BeastAttack              | Heretic            | N/A |
| A_SnakeAttack              | Heretic            | N/A |
| A_SnakeAttack2             | Heretic            | N/A |
| A_LichAttack               | Heretic            | Named A_HeadAttack in vanilla; renamed to avoid ambiguity with Cacodemon attack function (matches ZDoom's naming) |
| A_HeadIceImpact            | Heretic            | N/A |
| A_HeadFireGrow             | Heretic            | N/A |
| A_WhirlwindSeek            | Heretic            | N/A |
| A_ClinkAttack              | Heretic            | N/A |
| A_WizAtk1                  | Heretic            | N/A |
| A_WizAtk2                  | Heretic            | N/A |
| A_WizAtk3                  | Heretic            | N/A |
| A_GhostOff                 | Heretic            | N/A |
| A_ImpMeAttack              | Heretic            | N/A |
| A_ImpMsAttack              | Heretic            | N/A |
| A_ImpMsAttack2             | Heretic            | N/A |
| A_ImpDeath                 | Heretic            | N/A |
| A_ImpXDeath1               | Heretic            | N/A |
| A_ImpXDeath2               | Heretic            | N/A |
| A_ImpExplode               | Heretic            | N/A |
| A_KnightAttack             | Heretic            | N/A |
| A_DripBlood                | Heretic            | N/A |
| A_Sor1Chase                | Heretic            | N/A |
| A_Sor1Pain                 | Heretic            | N/A |
| A_Srcr1Attack              | Heretic            | N/A |
| A_SorZap                   | Heretic            | N/A |
| A_SorcererRise             | Heretic            | N/A |
| A_SorRise                  | Heretic            | N/A |
| A_SorSightSnd              | Heretic            | N/A |
| A_Srcr2Decide              | Heretic            | N/A |
| A_Srcr2Attack              | Heretic            | N/A |
| A_Sor2DthInit              | Heretic            | N/A |
| A_SorDSph                  | Heretic            | N/A |
| A_Sor2DthLoop              | Heretic            | N/A |
| A_SorDExp                  | Heretic            | N/A |
| A_SorDBon                  | Heretic            | N/A |
| A_BlueSpark                | Heretic            | N/A |
| A_GenWizard                | Heretic            | N/A |
| A_MinotaurAtk1             | Heretic            | N/A |
| A_MinotaurDecide           | Heretic            | N/A |
| A_MinotaurAtk2             | Heretic            | N/A |
| A_MinotaurAtk3             | Heretic            | N/A |
| A_MinotaurCharge           | Heretic            | N/A |
| A_MntrFloorFire            | Heretic            | N/A |
| A_FlameCheck               | Hexen              | N/A |
| A_Summon                   | Hexen              | N/A |
| A_ThrustInitUp             | Hexen              | N/A |
| A_ThrustInitDn             | Hexen              | N/A |
| A_ThrustRaise              | Hexen              | N/A |
| A_ThrustBlock              | Hexen              | N/A |
| A_ThrustImpale             | Hexen              | N/A |
| A_ThrustLower              | Hexen              | N/A |
| A_TeloSpawnC               | Hexen              | N/A |
| A_TeloSpawnB               | Hexen              | N/A |
| A_TeloSpawnA               | Hexen              | N/A |
| A_TeloSpawnD               | Hexen              | N/A |
| A_CheckTeleRing            | Hexen              | N/A |
| A_FogSpawn                 | Hexen              | N/A |
| A_FogMove                  | Hexen              | N/A |
| A_Quake                    | Hexen              | N/A |
| A_PoisonBagInit            | Hexen              | N/A |
| A_PoisonBagDamage          | Hexen              | N/A |
| A_PoisonBagCheck           | Hexen              | N/A |
| A_CheckThrowBomb           | Hexen              | N/A |
| A_NoGravity                | Hexen              | N/A |
| A_PotteryExplode           | Hexen              | N/A |
| A_PotteryChooseBit         | Hexen              | N/A |
| A_PotteryCheck             | Hexen              | N/A |
| A_CorpseBloodDrip          | Hexen              | N/A |
| A_CorpseExplode            | Hexen              | N/A |
| A_LeafSpawn                | Hexen              | N/A |
| A_LeafThrust               | Hexen              | N/A |
| A_LeafCheck                | Hexen              | N/A |
| A_BridgeInit               | Hexen              | N/A |
| A_BridgeOrbit              | Hexen              | N/A |
| A_TreeDeath                | Hexen              | N/A |
| A_PoisonShroom             | Hexen              | N/A |
| A_SoAExplode               | Hexen              | N/A |
| A_BellReset1               | Hexen              | N/A |
| A_BellReset2               | Hexen              | N/A |
| A_FSwordFlames             | Hexen              | N/A |
| A_CStaffMissileSlither     | Hexen              | N/A |
| A_CFlameRotate             | Hexen              | N/A |
| A_CFlamePuff               | Hexen              | N/A |
| A_CFlameMissile            | Hexen              | N/A |
| A_CHolySeek                | Hexen              | N/A |
| A_CHolyCheckScream         | Hexen              | N/A |
| A_CHolyTail                | Hexen              | N/A |
| A_CHolySpawnPuff           | Hexen              | N/A |
| A_CHolyAttack2             | Hexen              | N/A |
| A_LightningZap             | Hexen              | N/A |
| A_LightningClip            | Hexen              | N/A |
| A_LightningRemove          | Hexen              | N/A |
| A_LastZap                  | Hexen              | N/A |
| A_ZapMimic                 | Hexen              | N/A |
| A_MStaffWeave              | Hexen              | N/A |
| A_MStaffTrack              | Hexen              | N/A |
| A_SnoutAttack              | Hexen              | N/A |
| A_ShedShard                | Hexen              | N/A |
| A_FreezeDeath              | Hexen              | N/A |
| A_FreezeDeathChunks        | Hexen              | N/A |
| A_SpeedFade                | Hexen              | N/A |
| A_IceSetTics               | Hexen              | N/A |
| A_IceCheckHeadDone         | Hexen              | N/A |
| A_PigPain                  | Hexen              | N/A |
| A_PigLook                  | Hexen              | N/A |
| A_PigChase                 | Hexen              | N/A |
| A_PigAttack                | Hexen              | N/A |
| A_QueueCorpse              | Hexen              | N/A |
| A_CentaurAttack            | Hexen              | N/A |
| A_CentaurAttack2           | Hexen              | N/A |
| A_SetReflective            | Hexen              | N/A |
| A_CentaurDefend            | Hexen              | N/A |
| A_UnSetReflective          | Hexen              | N/A |
| A_CentaurDropStuff         | Hexen              | N/A |
| A_CheckFloor               | Hexen              | N/A |
| A_DemonAttack1             | Hexen              | N/A |
| A_DemonAttack2             | Hexen              | N/A |
| A_DemonDeath               | Hexen              | N/A |
| A_Demon2Death              | Hexen              | N/A |
| A_WraithRaiseInit          | Hexen              | N/A |
| A_WraithRaise              | Hexen              | N/A |
| A_WraithInit               | Hexen              | N/A |
| A_WraithLook               | Hexen              | N/A |
| A_WraithChase              | Hexen              | N/A |
| A_WraithFX3                | Hexen              | N/A |
| A_WraithMelee              | Hexen              | N/A |
| A_WraithMissile            | Hexen              | N/A |
| A_WraithFX2                | Hexen              | N/A |
| A_MinotaurFade1            | Hexen              | N/A |
| A_MinotaurFade2            | Hexen              | N/A |
| A_MinotaurLook             | Hexen              | N/A |
| A_MinotaurChase            | Hexen              | N/A |
| A_MinotaurRoam             | Hexen              | N/A |
| A_MinotaurAtk1             | Hexen              | N/A |
| A_MinotaurDecide           | Hexen              | N/A |
| A_MinotaurAtk2             | Hexen              | N/A |
| A_MinotaurAtk3             | Hexen              | N/A |
| A_MinotaurCharge           | Hexen              | N/A |
| A_SmokePuffExit            | Hexen              | N/A |
| A_MinotaurFade0            | Hexen              | N/A |
| A_MntrFloorFire            | Hexen              | N/A |
| A_SerpentChase             | Hexen              | N/A |
| A_SerpentHumpDecide        | Hexen              | N/A |
| A_SerpentUnHide            | Hexen              | N/A |
| A_SerpentRaiseHump         | Hexen              | N/A |
| A_SerpentLowerHump         | Hexen              | N/A |
| A_SerpentHide              | Hexen              | N/A |
| A_SerpentBirthScream       | Hexen              | N/A |
| A_SetShootable             | Hexen              | N/A |
| A_SerpentCheckForAttack    | Hexen              | N/A |
| A_UnSetShootable           | Hexen              | N/A |
| A_SerpentDiveSound         | Hexen              | N/A |
| A_SerpentWalk              | Hexen              | N/A |
| A_SerpentChooseAttack      | Hexen              | N/A |
| A_SerpentMeleeAttack       | Hexen              | N/A |
| A_SerpentMissileAttack     | Hexen              | N/A |
| A_SerpentHeadPop           | Hexen              | N/A |
| A_SerpentSpawnGibs         | Hexen              | N/A |
| A_SerpentHeadCheck         | Hexen              | N/A |
| A_FloatGib                 | Hexen              | N/A |
| A_DelayGib                 | Hexen              | N/A |
| A_SinkGib                  | Hexen              | N/A |
| A_BishopDecide             | Hexen              | N/A |
| A_BishopDoBlur             | Hexen              | N/A |
| A_BishopSpawnBlur          | Hexen              | N/A |
| A_BishopChase              | Hexen              | N/A |
| A_BishopAttack             | Hexen              | N/A |
| A_BishopAttack2            | Hexen              | N/A |
| A_BishopPainBlur           | Hexen              | N/A |
| A_BishopPuff               | Hexen              | N/A |
| A_SetAltShadow             | Hexen              | N/A |
| A_BishopMissileWeave       | Hexen              | N/A |
| A_BishopMissileSeek        | Hexen              | N/A |
| A_DragonInitFlight         | Hexen              | N/A |
| A_DragonFlap               | Hexen              | N/A |
| A_DragonFlight             | Hexen              | N/A |
| A_DragonAttack             | Hexen              | N/A |
| A_DragonPain               | Hexen              | N/A |
| A_DragonCheckCrash         | Hexen              | N/A |
| A_DragonFX2                | Hexen              | N/A |
| A_EttinAttack              | Hexen              | N/A |
| A_DropMace                 | Hexen              | N/A |
| A_FiredRocks               | Hexen              | N/A |
| A_UnSetInvulnerable        | Hexen              | N/A |
| A_FiredChase               | Hexen              | N/A |
| A_FiredAttack              | Hexen              | N/A |
| A_FiredSplotch             | Hexen              | N/A |
| A_SmBounce                 | Hexen              | N/A |
| A_IceGuyLook               | Hexen              | N/A |
| A_IceGuyChase              | Hexen              | N/A |
| A_IceGuyAttack             | Hexen              | N/A |
| A_IceGuyDie                | Hexen              | N/A |
| A_IceGuyMissilePuff        | Hexen              | N/A |
| A_IceGuyMissileExplode     | Hexen              | N/A |
| A_ClassBossHealth          | Hexen              | N/A |
| A_FastChase                | Hexen              | N/A |
| A_FighterAttack            | Hexen              | N/A |
| A_ClericAttack             | Hexen              | N/A |
| A_MageAttack               | Hexen              | N/A |
| A_SorcSpinBalls            | Hexen              | N/A |
| A_SpeedBalls               | Hexen              | N/A |
| A_SpawnFizzle              | Hexen              | N/A |
| A_SorcBossAttack           | Hexen              | N/A |
| A_SorcBallOrbit            | Hexen              | N/A |
| A_SorcBallPop              | Hexen              | N/A |
| A_BounceCheck              | Hexen              | N/A |
| A_SorcFX1Seek              | Hexen              | N/A |
| A_SorcFX2Split             | Hexen              | N/A |
| A_SorcFX2Orbit             | Hexen              | N/A |
| A_SorcererBishopEntry      | Hexen              | N/A |
| A_SpawnBishop              | Hexen              | N/A |
| A_SorcFX4Check             | Hexen              | N/A |
| A_KoraxStep2               | Hexen              | N/A |
| A_KoraxChase               | Hexen              | N/A |
| A_KoraxStep                | Hexen              | N/A |
| A_KoraxDecide              | Hexen              | N/A |
| A_KoraxMissile             | Hexen              | N/A |
| A_KoraxCommand             | Hexen              | N/A |
| A_KoraxBonePop             | Hexen              | N/A |
| A_KSpiritRoam              | Hexen              | N/A |
| A_KBoltRaise               | Hexen              | N/A |
| A_KBolt                    | Hexen              | N/A |
| A_BatSpawnInit             | Hexen              | N/A |
| A_BatSpawn                 | Hexen              | N/A |
| A_BatMove                  | Hexen              | N/A |


### Actions - Extended

Brand-new actions defined by Raven EX.

| Name                                           | Origin | Description |
|------------------------------------------------|--------|-------------|
| [A_PlaySound](#A_PlaySound)                    | MBF    | `TODO-PROP-DESC` |
| [A_FaceTargetSound](#A_FaceTargetSound)        | New    | `TODO-PROP-DESC` |
| [A_RandomJump](#A_RandomJump)                  | MBF    | `TODO-PROP-DESC` |
| [A_Die](#A_Die)                                | MBF    | `TODO-PROP-DESC` |
| [A_Stop](#A_Stop)                              | MBF    | `TODO-PROP-DESC` |
| [A_SpawnObject](#A_SpawnObject)                | MBF21  | `TODO-PROP-DESC` |
| [A_MonsterProjectile](#A_MonsterProjectile)    | MBF21  | `TODO-PROP-DESC` |
| [A_MonsterBulletAttack](#A_MonsterBulletAttack)| MBF21  | `TODO-PROP-DESC` |
| [A_MonsterMeleeAttack](#A_MonsterMeleeAttack)  | MBF21  | `TODO-PROP-DESC` |
| [A_MonsterComboAttack](#A_MonsterComboAttack)  | New    | `TODO-PROP-DESC` |
| [A_SpawnMinion](#A_SpawnMinion)                | New    | `TODO-PROP-DESC` |
| [A_RadiusDamage](#A_RadiusDamage)              | MBF21  | `TODO-PROP-DESC` |
| [A_NoiseAlert](#A_NoiseAlert)                  | MBF21  | `TODO-PROP-DESC` |
| [A_HealChase](#A_HealChase)                    | MBF21  | `TODO-PROP-DESC` |
| [A_SeekTracer](#A_SeekTracer)                  | MBF21  | `TODO-PROP-DESC` |
| [A_FindTracer](#A_FindTracer)                  | MBF21  | `TODO-PROP-DESC` |
| [A_ClearTracer](#A_ClearTracer)                | MBF21  | `TODO-PROP-DESC` |
| [A_MissileExplode](#A_MissileExplode)          | New    | `TODO-PROP-DESC` |
| [A_MissileCountdown](#A_MissileCountdown)      | New    | `TODO-PROP-DESC` |
| [A_JumpIfHealthBelow](#A_JumpIfHealthBelow)    | MBF21  | `TODO-PROP-DESC` |
| [A_JumpIfTargetInSight](#A_JumpIfTargetInSight)| MBF21  | `TODO-PROP-DESC` |
| [A_JumpIfTargetCloser](#A_JumpIfTargetCloser)  | MBF21  | `TODO-PROP-DESC` |
| [A_JumpIfTargetAlive](#A_JumpIfTargetAlive)    | New    | `TODO-PROP-DESC` |
| [A_JumpIfTracerInSight](#A_JumpIfTracerInSight)| MBF21  | `TODO-PROP-DESC` |
| [A_JumpIfTracerCloser](#A_JumpIfTracerCloser)  | MBF21  | `TODO-PROP-DESC` |
| [A_JumpIfTracerAlive](#A_JumpIfTracerAlive)    | New    | `TODO-PROP-DESC` |
| [A_JumpIfHasPower](#A_JumpIfHasPower)          | New    | `TODO-PROP-DESC` |
| [A_JumpIfHasArtifact](#A_JumpIfHasArtifact)    | New    | `TODO-PROP-DESC` |
| [A_JumpIfHasKey](#A_JumpIfHasKey)              | New    | `TODO-PROP-DESC` |
| [A_JumpIfWeaponOwned](#A_JumpIfWeaponOwned)    | New    | `TODO-PROP-DESC` |
| [A_JumpIfWeaponUsable](#A_JumpIfWeaponUsable)  | New    | `TODO-PROP-DESC` |
| [A_JumpIfWeaponPiece](#A_JumpIfWeaponPiece)    | New    | `TODO-PROP-DESC` |
| [A_DeathBallBounceEX](#A_DeathBallBounceEX)    | New    | `TODO-PROP-DESC` |
| [A_TempestChain](#A_TempestChain)              | New    | `TODO-PROP-DESC` |
| [A_TempestSpray](#A_TempestSpray)              | New    | `TODO-PROP-DESC` |
| [A_KnightAttackEX](#A_KnightAttackEX)          | New    | `TODO-PROP-DESC` |
| [A_ClinkRushEX](#A_ClinkRushEX)                | New    | `TODO-PROP-DESC` |
| [A_ClinkAttackEX](#A_ClinkAttackEX)            | New    | `TODO-PROP-DESC` |
| [A_MinotaurMeleeEX](#A_MinotaurMeleeEX)        | New    | `TODO-PROP-DESC` |
| [A_TrollChargeStart](#A_TrollChargeStart)      | New    | `TODO-PROP-DESC` |
| [A_TrollCharge](#A_TrollCharge)                | New    | `TODO-PROP-DESC` |
| [A_SuperDemonAttack1](#A_SuperDemonAttack1)    | New    | `TODO-PROP-DESC` |
| [A_SuperDemonAttack2](#A_SuperDemonAttack2)    | New    | `TODO-PROP-DESC` |
| [A_CheckThrowBomb2](#A_CheckThrowBomb2)        | New    | `TODO-PROP-DESC` |
| [A_ShedShardEX](#A_ShedShardEX)                | New    | Improved Frost Shards split function. Splits the shard into a number of child shards, with mathematically correct damage distribution. |
| [A_CentaurEXGuardRaise](#A_CentaurEXGuardRaise)| New    | New centaur shield behavior. Guards and reflects attacks within a limited angle, instead of a full 360 degrees. |
| [A_CentaurEXGuardHold](#A_CentaurEXGuardHold)  | New    | New centaur shield behavior. Decides whether to lower the shield and perform a melee attack. |
| [A_CentaurEXGuardLower](#A_CentaurEXGuardLower)| New    | New centaur shield behavior. Lowers the shield, ending the guard. |
| [A_CentaurAttack2EX](#A_CentaurAttack2EX)      | New    | Improved Slaughtaur projectile attack. If player is within melee range, attempt to melee attack the player instead. |
| [A_SerpentHumpDecideEX](#A_SerpentHumpDecideEX)| New    | Fixed version of A_SerpentHumpDecide. Checks if its `target` is in sight before attempting a ranged attack. |
| [A_BishopSpawnBlurEX](#A_BishopSpawnBlurEX)    | New    | Fixed version of A_BishopSpawnBlur. Checks if its `target` is in sight before attempting a ranged attack. |
| [A_FlyTarget](#A_FlyTarget)                    | New    | Same behavior as ZDoom's A_FlySearch; name taken from original source code |
| [A_FlyChase](#A_FlyChase)                      | New    | Same behavior as ZDoom's A_FlyBuzz; name taken from original source code |

## States

`TODO-SUMMARY`

### Properties

| Name        | Type         | Description |
|-------------|--------------|-------------|
| sprite      | sprite       | `TODO-PROP-DESC` |
| frame       | frame        | `TODO-PROP-DESC`  NOTE: need to document special cases |
| tics        | integer      | `TODO-PROP-DESC` |
| seconds     | decimal      | `TODO-PROP-DESC` |
| next        | state        | `TODO-PROP-DESC` NOTE: need to document special cases |
| flags       | state flags  | `TODO-PROP-DESC` |
| addflags    | state flags  | `TODO-PROP-DESC` |
| removeflags | state flags  | `TODO-PROP-DESC` |
| action      | state action | `TODO-PROP-DESC` |
| args        | args         | `TODO-PROP-DESC` |
| offsetx     | integer      | `TODO-PROP-DESC` |
| offsety     | integer      | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name          | Origin | Description |
|---------------|--------|-------------|
| SKILL5FAST    | MBF21  | Frame duration is halved on Skill 5 |
| INTERPOLATE   | New    | Interpolate weapon offsets (i.e. misc1/misc2 fields) -- Note for ZDoom: H+H state offset interpolation is a lerp over the entire duration of the state, not just 1 tic |
| NOINTERPOLATE | New    | Do not interpolate weapon offsets, even if weapon has the WF_INTERPOLATE flag set |

## Player class

`TODO-SUMMARY`

### Properties

| Name              | Type                   | Description |
|-------------------|------------------------|-------------|
| actor             | actor                  | `TODO-PROP-DESC` |
| displayname       | string                 | `TODO-PROP-DESC` |
| flags             | playerclass flags      | `TODO-PROP-DESC` |
| addflags          | playerclass flags      | `TODO-PROP-DESC` |
| removeflags       | playerclass flags      | `TODO-PROP-DESC` |
| spawnfilter       | mapthing flags         | `TODO-PROP-DESC` |
| speedfactor       | decimal                | `TODO-PROP-DESC` |
| forwardwalkspeed  | integer                | `TODO-PROP-DESC` |
| forwardrunspeed   | integer                | `TODO-PROP-DESC` |
| forwardairspeed   | decimal                | `TODO-PROP-DESC` |
| sidewalkspeed     | integer                | `TODO-PROP-DESC` |
| siderunspeed      | integer                | `TODO-PROP-DESC` |
| sideairspeed      | decimal                | `TODO-PROP-DESC` |
| powerspeedfactor  | decimal                | `TODO-PROP-DESC` |
| speedtrailactor   | actor                  | `TODO-PROP-DESC` |
| viewheight        | decimal                | `TODO-PROP-DESC` |
| jumpvelocity      | decimal                | `TODO-PROP-DESC` |
| actorstepheight   | decimal                | `TODO-PROP-DESC` |
| invulncolormap    | string                 | `TODO-PROP-DESC` |
| sounds            | playerclass.sounds     | `TODO-PROP-DESC` |
| states            | playerclass.states     | `TODO-PROP-DESC` |
| actions           | playerclass.actions    | `TODO-PROP-DESC` |
| hexenarmor        | playerclass.hexenarmor | `TODO-PROP-DESC` |
| menubackground    | string                 | `TODO-PROP-DESC` |
| menusprite        | string                 | `TODO-PROP-DESC` |
| menunumframes     | integer                | `TODO-PROP-DESC` |
| oof               | sound                  | `TODO-PROP-DESC` |
| grunt             | sound                  | `TODO-PROP-DESC` |
| falling           | sound                  | `TODO-PROP-DESC` |
| noway             | sound                  | `TODO-PROP-DESC` |
| keytry            | sound                  | `TODO-PROP-DESC` |
| puzzlefail        | sound                  | `TODO-PROP-DESC` |
| pain              | sound                  | `TODO-PROP-DESC` |
| death             | sound                  | `TODO-PROP-DESC` |
| wimpydeath        | sound                  | `TODO-PROP-DESC` |
| crazydeath        | sound                  | `TODO-PROP-DESC` |
| gibdeath1         | sound                  | `TODO-PROP-DESC` |
| gibdeath2         | sound                  | `TODO-PROP-DESC` |
| gibdeath3         | sound                  | `TODO-PROP-DESC` |
| idle              | state                  | `TODO-PROP-DESC` |
| run               | state                  | `TODO-PROP-DESC` |
| attack1           | state                  | `TODO-PROP-DESC` |
| attack2           | state                  | `TODO-PROP-DESC` |
| firedeath         | state                  | `TODO-PROP-DESC` |
| firedeathend      | state                  | `TODO-PROP-DESC` |
| flechette         | artifact action        | `TODO-PROP-DESC` |
| healingradius     | artifact action        | `TODO-PROP-DESC` |
| healingradiusargs | args                   | `TODO-PROP-DESC` |
| invulnstart       | player action          | `TODO-PROP-DESC` |
| invulntick        | player action          | `TODO-PROP-DESC` |
| invulndraw        | playervis action       | `TODO-PROP-DESC` |
| invulnend         | player action          | `TODO-PROP-DESC` |
| max               | integer                | `TODO-PROP-DESC` |
| base              | integer                | `TODO-PROP-DESC` |
| piecearmor        | integer                | `TODO-PROP-DESC` |
| pieceshield       | integer                | `TODO-PROP-DESC` |
| piecehelmet       | integer                | `TODO-PROP-DESC` |
| pieceamulet       | integer                | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name           | Origin | Description |
|----------------|--------|-------------|
| MENUSELECTABLE | New    | Whether or not this class is selectable via new game menu |
| WEAPONBOB      | New    | Allow weapon bobbing |
| WEAPONLOWER    | New    | Allow weapon lowering |
| AUTOUSEHEALTH  | New    | Allow auto-use health |
| CHICKENTWITCH  | New    | Does involuntary chicken actions, such as twitching, jumping, and making noise. |

### Actions

| Name                                               | Origin | Description |
|----------------------------------------------------|--------|-------------|
| [A_InvulnStandardStart](#A_InvulnStandardStart)    | New    | `TODO-ACTION-DESC` |
| [A_InvulnStandardEnd](#A_InvulnStandardEnd)        | New    | `TODO-ACTION-DESC` |
| [A_InvulnPhasedTick](#A_InvulnPhasedTick)          | New    | `TODO-ACTION-DESC` |
| [A_InvulnPhasedDraw](#A_InvulnPhasedDraw)          | New    | `TODO-ACTION-DESC` NOTE: this is the weird PlayerVis one |
| [A_InvulnPhasedEnd](#A_InvulnPhasedEnd)            | New    | `TODO-ACTION-DESC` |
| [A_InvulnReflectiveStart](#A_InvulnReflectiveStart)| New    | `TODO-ACTION-DESC` |
| [A_InvulnReflectiveEnd](#A_InvulnReflectiveEnd)    | New    | `TODO-ACTION-DESC` |

## Weapon

`TODO-SUMMARY`

### Properties

| Name                 | Type         | Description |
|----------------------|--------------|-------------|
| ammo                 | ammo         | `TODO-PROP-DESC` |
| ammo2                | ammo         | `TODO-PROP-DESC` |
| selectstate          | state        | `TODO-PROP-DESC` |
| deselectstate        | state        | `TODO-PROP-DESC` |
| readystate           | state        | `TODO-PROP-DESC` |
| firestate            | state        | `TODO-PROP-DESC` |
| holdstate            | state        | `TODO-PROP-DESC` |
| altfirestate         | state        | `TODO-PROP-DESC` |
| altholdstate         | state        | `TODO-PROP-DESC` |
| flashstate           | state        | `TODO-PROP-DESC` |
| customstates         | object       | `TODO-PROP-DESC` |
| flags                | weapon flags | `TODO-PROP-DESC` |
| addflags             | weapon flags | `TODO-PROP-DESC` |
| removeflags          | weapon flags | `TODO-PROP-DESC` |
| ammopershot          | integer      | `TODO-PROP-DESC` |
| ammopershot2         | integer      | `TODO-PROP-DESC` |
| slot                 | integer      | `TODO-PROP-DESC` |
| slotpriority         | integer      | `TODO-PROP-DESC` |
| switchpriority       | integer      | `TODO-PROP-DESC` |
| initialowned         | integer      | `TODO-PROP-DESC` |
| initialraised        | integer      | `TODO-PROP-DESC` |
| carouselicon         | string       | `TODO-PROP-DESC` |
| fullscreenoffset     | integer      | `TODO-PROP-DESC` |
| playerclass          | playerclass  | `TODO-PROP-DESC` |
| numpieces            | integer      | `TODO-PROP-DESC` |
| pickupmessage        | string       | `TODO-PROP-DESC` |
| piecepickupmessage   | string       | `TODO-PROP-DESC` |
| pieceassemblymessage | string       | `TODO-PROP-DESC` |
| weaponlevel2         | weapon       | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

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

### Actions - Base

Actions (a.k.a. 'codepointers') from the base vanilla games. Unless otherwise mentioned, these functions behave identically to their vanilla counterparts.

| Name                                   | Origin            |
|----------------------------------------|-------------------|
| A_Light0                               | Common            |
| A_Light1                               | Common            |
| A_Light2                               | Common            |
| A_WeaponReady                          | Common            |
| A_Lower                                | Common            |
| A_Raise                                | Common            |
| A_ReFire                               | Common            |
| A_StaffAttackPL1                       | Heretic           |
| A_StaffAttackPL2                       | Heretic           |
| A_BeakReady                            | Heretic           |
| A_BeakRaise                            | Heretic           |
| A_BeakAttackPL1                        | Heretic           |
| A_BeakAttackPL2                        | Heretic           |
| A_GauntletAttack                       | Heretic           |
| A_FireBlasterPL1                       | Heretic           |
| A_FireBlasterPL2                       | Heretic           |
| A_FireMacePL1                          | Heretic           |
| A_FireMacePL2                          | Heretic           |
| A_FireSkullRodPL1                      | Heretic           |
| A_FireSkullRodPL2                      | Heretic           |
| A_FireGoldWandPL1                      | Heretic           |
| A_FireGoldWandPL2                      | Heretic           |
| A_FirePhoenixPL1                       | Heretic           |
| A_InitPhoenixPL2                       | Heretic           |
| A_FirePhoenixPL2                       | Heretic           |
| A_ShutdownPhoenixPL2                   | Heretic           |
| A_FireCrossbowPL1                      | Heretic           |
| A_FireCrossbowPL2                      | Heretic           |
| A_FPunchAttack                         | Hexen             |
| A_FAxeAttack                           | Hexen             |
| A_SnoutAttack                          | Hexen             |
| A_FHammerAttack                        | Hexen             |
| A_FHammerThrow                         | Hexen             |
| A_FSwordAttack                         | Hexen             |
| A_CMaceAttack                          | Hexen             |
| A_CStaffInitBlink                      | Hexen             |
| A_CStaffCheckBlink                     | Hexen             |
| A_CStaffCheck                          | Hexen             |
| A_CStaffAttack                         | Hexen             |
| A_CFlameAttack                         | Hexen             |
| A_CHolyAttack                          | Hexen             |
| A_CHolyPalette                         | Hexen             |
| A_MWandAttack                          | Hexen             |
| A_LightningReady                       | Hexen             |
| A_MLightningAttack                     | Hexen             |
| A_MStaffAttack                         | Hexen             |
| A_MStaffPalette                        | Hexen             |
| A_FireConePL1                          | Hexen             |

### Actions - Extended

Brand-new actions defined by Raven EX.

| Name                                                     | Origin | Description |
|----------------------------------------------------------|--------|-|
| [A_WeaponSound](#A_WeaponSound)                          | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponProjectile](#A_WeaponProjectile)                | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponBulletAttack](#A_WeaponBulletAttack)            | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponMeleeAttack](#A_WeaponMeleeAttack)              | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponJump](#A_WeaponJump)                            | MBF21  | `TODO-ACTION-DESC` |
| [A_ConsumeAmmo](#A_ConsumeAmmo)                          | MBF21  | `TODO-ACTION-DESC` |
| [A_CheckAmmo](#A_CheckAmmo)                              | MBF21  | `TODO-ACTION-DESC` |
| [A_RefireTo](#A_RefireTo)                                | MBF21  | `TODO-ACTION-DESC` |
| [A_GunFlashTo](#A_GunFlashTo)                            | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponAlert](#A_WeaponAlert)                          | MBF21  | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfHasPower](#A_WeaponJumpIfHasPower)        | New    | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfHasArtifact](#A_WeaponJumpIfHasArtifact)  | New    | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfHasKey](#A_WeaponJumpIfHasKey)            | New    | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfWeaponOwned](#A_WeaponJumpIfWeaponOwned)  | New    | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfWeaponUsable](#A_WeaponJumpIfWeaponUsable)| New    | `TODO-ACTION-DESC` |
| [A_WeaponJumpIfWeaponPiece](#A_WeaponJumpIfWeaponPiece)  | New    | `TODO-ACTION-DESC` |
| [A_GauntletAttackEX](#A_GauntletAttackEX)                | New    | `TODO-ACTION-DESC` |
| [A_FireGoldWandEX1](#A_FireGoldWandEX1)                  | New    | `TODO-ACTION-DESC` |
| [A_FireCrossbowEX1](#A_FireCrossbowEX1)                  | New    | `TODO-ACTION-DESC` |
| [A_FireCrossbowEX2](#A_FireCrossbowEX2)                  | New    | `TODO-ACTION-DESC` |
| [A_FireSkullRodEX1](#A_FireSkullRodEX1)                  | New    | `TODO-ACTION-DESC` |
| [A_FireSkullRodEX2](#A_FireSkullRodEX2)                  | New    | `TODO-ACTION-DESC` |
| [A_FirePhoenixEX2](#A_FirePhoenixEX2)                    | New    | `TODO-ACTION-DESC` |
| [A_FireMaceEX1](#A_FireMaceEX1)                          | New    | `TODO-ACTION-DESC` |
| [A_FireMaceEX2](#A_FireMaceEX2)                          | New    | `TODO-ACTION-DESC` |
| [A_FireTempestWandPL1](#A_FireTempestWandPL1)            | New    | `TODO-ACTION-DESC` |
| [A_FAxeCheckUp](#A_FAxeCheckUp)                          | New    | Same behavior as ZDoom's function of the same name. |
| [A_FAxeCheckUpG](#A_FAxeCheckUpG)                        | New    | Same behavior as ZDoom's function of the same name. |
| [A_FAxeCheckReady](#A_FAxeCheckReady)                    | New    | Same behavior as ZDoom's function of the same name. |
| [A_FAxeCheckReadyG](#A_FAxeCheckReadyG)                  | New    | Same behavior as ZDoom's function of the same name. |
| [A_FAxeCheckAtk](#A_FAxeCheckAtk)                        | New    | Same behavior as ZDoom's function of the same name. |
| [A_CMaceAttackEX](#A_CMaceAttackEX)                      | New    | Parameterized Mace of Contrition attack. Inflicts bonus damage on actors stunned by a shield parry. |
| [A_CShieldParry](#A_CShieldParry)                        | New    | Cleric shield parry. Stuns melee attackers and reflects projectiles. |
| [A_CShieldGuard](#A_CShieldGuard)                        | New    | Cleric shield block. Reduces damage from attacks within a certain angle. |
| [A_CShieldLower](#A_CShieldLower)                        | New    | Ends cleric shield parry & block behavior. |
| [A_CStaffDrainEX](#A_CStaffDrainEX)                      | New    | Parameterized Serpent Staff health-drain attack. |
| [A_FireConeEX](#A_FireConeEX)                            | New    | Parameterized Frost Shards attack. |
| [A_MStaffAttackEX](#A_MStaffAttackEX)                    | New    | Parameterized Bloodscourge attack. |

## Ammo

`TODO-SUMMARY`

### Properties

| Name                 | Type    | Description |
|----------------------|---------|-------------|
| clipammo             | integer | `TODO-PROP-DESC` |
| maxammo              | integer | `TODO-PROP-DESC` |
| initialammo          | integer | `TODO-PROP-DESC` |
| maxupgradedammo      | integer | `TODO-PROP-DESC` |
| boxammo              | integer | `TODO-PROP-DESC` |
| backpackammo         | integer | `TODO-PROP-DESC` |
| weaponammo           | integer | `TODO-PROP-DESC` |
| weaponpieceammo      | integer | `TODO-PROP-DESC` |
| deathmatchweaponammo | integer | `TODO-PROP-DESC` |
| skill1multiplier     | decimal | `TODO-PROP-DESC` |
| skill2multiplier     | decimal | `TODO-PROP-DESC` |
| skill3multiplier     | decimal | `TODO-PROP-DESC` |
| skill4multiplier     | decimal | `TODO-PROP-DESC` |
| skill5multiplier     | decimal | `TODO-PROP-DESC` |
| icon                 | string  | `TODO-PROP-DESC` |

## Puff

`TODO-SUMMARY`

### Properties

| Name            | Type       | Description |
|-----------------|------------|-------------|
| thingtype       | actor      | `TODO-PROP-DESC` |
| sound           | sound      | `TODO-PROP-DESC` |
| misssound       | sound      | `TODO-PROP-DESC` |
| hitsound        | sound      | `TODO-PROP-DESC` |
| hitpufftype     | actor      | `TODO-PROP-DESC` |
| nobloodpufftype | actor      | `TODO-PROP-DESC` |
| upspeed         | decimal    | `TODO-PROP-DESC` |
| zspread         | decimal    | `TODO-PROP-DESC` |
| flags           | puff flags | `TODO-PROP-DESC` |
| addflags        | puff flags | `TODO-PROP-DESC` |
| removeflags     | puff flags | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name          | Origin   | Description |
|---------------|----------|-------------|
| PUFFONACTORS  | New      | always puff on actors -- NOTE: this behavior is a bit redundant with the hitpufftype field; this flag is defined for parity with EE |
| BLOODLESS     | New      | don't spawn blood when hitting a bleeding target |
| LOCALTHRUST   | New      | apply damage thrust from puff's position, not shooter's |
| RANDOMTICS    | New      | randomize first frame duration |
| TARGETSHOOTER | Reserved | set puff's target to the shooter, like projectiles -- NOTE: this behavior is always enforced; this flag is defined for parity with EE |
| ALTBLOOD      | New      | use alternative blood splat (formerly hardcoded to the Fighter Axe) |

## Pickup

`TODO-SUMMARY`

### Properties

| Name        | Type          | Description |
|-------------|---------------|-------------|
| sound       | sound         | `TODO-PROP-DESC` |
| message     | string        | `TODO-PROP-DESC` |
| flashtics   | integer       | `TODO-PROP-DESC` |
| action      | pickup action | `TODO-PROP-DESC` |
| args        | args          | `TODO-PROP-DESC` |
| flags       | pickup flags  | `TODO-PROP-DESC` |
| addflags    | pickup flags  | `TODO-PROP-DESC` |
| removeflags | pickup flags  | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name            | Origin | Description |
|-----------------|--------|-------------|
| ALWAYSPICKUP    | New    | always pick up this item, even if the player already has everything it attempts to grant |
| MULTIPLAYERSTAY | New    | pickup mobj stays behind on pickup in multiplayer; overrules ALWAYSPICKUP in if both are set. |
| WEAPONSTAY      | New    | pickup mobj stays behind on pickup if the multiplayer gamemode uses "weapon stay" semantics (many ports have explicit options for this; in vanilla, this means non-altdeath deathmatch) |
| ARTIFLASH       | New    | pickup mobj plays the artifact flash animation on pickup, instead of removing the mobj. |
| INSTANCED       | New    | pickup is instanced for each player in multiplayer modes -- this is a Kex multiplayer feature; something nice to have for other MP ports but they gotta implement it first |

### Actions

| Name                                       | Origin | Description |
|--------------------------------------------|--------|-------------|
| [A_PickupHealth](#A_PickupHealth)          | New    | `TODO-ACTION-DESC` |
| [A_PickupBasicArmor](#A_PickupBasicArmor)  | New    | `TODO-ACTION-DESC` |
| [A_PickupHexenArmor](#A_PickupHexenArmor)  | New    | `TODO-ACTION-DESC` |
| [A_PickupBackpack](#A_PickupBackpack)      | New    | `TODO-ACTION-DESC` |
| [A_PickupWeapon](#A_PickupWeapon)          | New    | `TODO-ACTION-DESC` |
| [A_PickupWeaponPiece](#A_PickupWeaponPiece)| New    | `TODO-ACTION-DESC` |
| [A_PickupAmmo](#A_PickupAmmo)              | New    | `TODO-ACTION-DESC` |
| [A_PickupDualAmmo](#A_PickupDualAmmo)      | New    | `TODO-ACTION-DESC` |
| [A_PickupPower](#A_PickupPower)            | New    | `TODO-ACTION-DESC` |
| [A_PickupArtifact](#A_PickupArtifact)      | New    | `TODO-ACTION-DESC` |
| [A_PickupKey](#A_PickupKey)                | New    | `TODO-ACTION-DESC` |

## Power

`TODO-SUMMARY`

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name        | Origin | Description |
|-------------|--------|-------------|
| MORPHIMMUNE | New    | power grants immunity to being morphed |
| MORPHRESET  | New    | remove this power when being morphed |
| CHANGERESET | New    | remove this power when voluntarily changing classes (e.g. via the "Hexen Chronicle" in the enhanced maps) |

### Properties

| Name                  | Type         | Description |
|-----------------------|--------------|-------------|
| durationtics          | integer      | `TODO-PROP-DESC` |
| durationseconds       | decimal      | `TODO-PROP-DESC` |
| blinkthresholdtics    | integer      | `TODO-PROP-DESC` |
| blinkthresholdseconds | decimal      | `TODO-PROP-DESC` |
| renewthresholdtics    | integer      | `TODO-PROP-DESC` |
| renewthresholdseconds | decimal      | `TODO-PROP-DESC` |
| startaction           | power action | `TODO-PROP-DESC` |
| tickaction            | power action | `TODO-PROP-DESC` |
| endaction             | power action | `TODO-PROP-DESC` |
| flags                 | power flags  | `TODO-PROP-DESC` |
| addflags              | power flags  | `TODO-PROP-DESC` |
| removeflags           | power flags  | `TODO-PROP-DESC` |

### Actions

| Name                                                 | Origin | Description |
|------------------------------------------------------|--------|-------------|
| [A_PowerInvulnerableStart](#A_PowerInvulnerableStart)| New    | `TODO-ACTION-DESC` |
| [A_PowerInvulnerableTick](#A_PowerInvulnerableTick)  | New    | `TODO-ACTION-DESC` |
| [A_PowerInvulnerableEnd](#A_PowerInvulnerableEnd)    | New    | `TODO-ACTION-DESC` |
| [A_PowerInvisibilityStart](#A_PowerInvisibilityStart)| New    | `TODO-ACTION-DESC` |
| [A_PowerInvisibilityEnd](#A_PowerInvisibilityEnd)    | New    | `TODO-ACTION-DESC` |
| [A_PowerStrengthStart](#A_PowerStrengthStart)        | New    | `TODO-ACTION-DESC` |
| [A_PowerFlightStart](#A_PowerFlightStart)            | New    | `TODO-ACTION-DESC` |
| [A_PowerFlightEnd](#A_PowerFlightEnd)                | New    | `TODO-ACTION-DESC` |
| [A_PowerWeaponLevel2Start](#A_PowerWeaponLevel2Start)| New    | `TODO-ACTION-DESC` |
| [A_PowerWeaponLevel2End](#A_PowerWeaponLevel2End)    | New    | `TODO-ACTION-DESC` |

## Artifact

`TODO-SUMMARY`

### Properties

| Name        | Type            | Description |
|-------------|-----------------|-------------|
| displayname | string          | `TODO-PROP-DESC` |
| icon        | string          | `TODO-PROP-DESC` |
| maxamount   | integer         | `TODO-PROP-DESC` |
| carryover   | integer         | `TODO-PROP-DESC` |
| usesound    | sound           | `TODO-PROP-DESC` |
| action      | artifact action | `TODO-PROP-DESC` |
| args        | args            | `TODO-PROP-DESC` |
| flags       | artifact flags  | `TODO-PROP-DESC` |
| addflags    | artifact flags  | `TODO-PROP-DESC` |
| removeflags | artifact flags  | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Name                | Origin | Description |
|---------------------|--------|-------------|
| AUTOUSE_HEALTH_BABY | New    | automatically-used health on baby difficulty |
| AUTOUSE_HEALTH_DM   | New    | automatically-used health artifact on deathmath |

### Actions

| Name                                         | Origin | Description |
|----------------------------------------------|--------|-------------|
| [A_ArtiGivePower](#A_ArtiGivePower)          | New    | `TODO-ACTION-DESC` |
| [A_ArtiHealth](#A_ArtiHealth)                | New    | `TODO-ACTION-DESC` |
| [A_ArtiEgg](#A_ArtiEgg)                      | New    | `TODO-ACTION-DESC` |
| [A_ArtiSummon](#A_ArtiSummon)                | New    | `TODO-ACTION-DESC` |
| [A_ArtiTeleport](#A_ArtiTeleport)            | New    | `TODO-ACTION-DESC` |
| [A_ArtiTeleportOther](#A_ArtiTeleportOther)  | New    | `TODO-ACTION-DESC` |
| [A_ArtiBoostMana](#A_ArtiBoostMana)          | New    | `TODO-ACTION-DESC` |
| [A_ArtiBoostArmor](#A_ArtiBoostArmor)        | New    | `TODO-ACTION-DESC` |
| [A_ArtiBlastRadius](#A_ArtiBlastRadius)      | New    | `TODO-ACTION-DESC` |
| [A_ArtiFlechette](#A_ArtiFlechette)          | New    | `TODO-ACTION-DESC` |
| [A_ArtiThrowingBomb](#A_ArtiThrowingBomb)    | New    | `TODO-ACTION-DESC` |
| [A_ArtiThrowingBombEX](#A_ArtiThrowingBombEX)| New    | `TODO-ACTION-DESC` |
| [A_ArtiPoisonBag](#A_ArtiPoisonBag)          | New    | `TODO-ACTION-DESC` |
| [A_ArtiFireBomb](#A_ArtiFireBomb)            | New    | `TODO-ACTION-DESC` |
| [A_ArtiHealingRadius](#A_ArtiHealingRadius)  | New    | `TODO-ACTION-DESC` |
| [A_RadiusGiveArmor](#A_RadiusGiveArmor)      | New    | `TODO-ACTION-DESC` |
| [A_RadiusGiveArmorEX](#A_RadiusGiveArmorEX)  | New    | `TODO-ACTION-DESC` |
| [A_RadiusGiveHealth](#A_RadiusGiveHealth)    | New    | `TODO-ACTION-DESC` |
| [A_RadiusGiveMana](#A_RadiusGiveMana)        | New    | `TODO-ACTION-DESC` |
| [A_RadiusGiveManaEX](#A_RadiusGiveManaEX)    | New    | `TODO-ACTION-DESC` |
| [A_ArtiPuzzleItem](#A_ArtiPuzzleItem)        | New    | `TODO-ACTION-DESC` |

## Lock

`TODO-SUMMARY`

### Properties

| Name              | Type    | Description |
|-------------------|---------|-------------|
| lockdoormessage   | string  | `TODO-PROP-DESC` |
| lockswitchmessage | string  | `TODO-PROP-DESC` |
| keypickupmessage  | string  | `TODO-PROP-DESC` |
| keyicon           | string  | `TODO-PROP-DESC` |
| color             | integer | `TODO-PROP-DESC` |

## Sound

`TODO-SUMMARY`

### Properties

| Name     | Type           | Description |
|----------|----------------|-------------|
| lump     | string         | `TODO-PROP-DESC` |
| alias    | string         | `TODO-PROP-DESC` |
| tactile  | sound.tactile  | `TODO-PROP-DESC` |
| channel  | channel flags  | `TODO-PROP-DESC` |
| position | position flags | `TODO-PROP-DESC` |
| volume   | integer        | `TODO-PROP-DESC` |

## Ambient

`TODO-SUMMARY`

### Properties

| Name      | Type    | Description |
|-----------|---------|-------------|
| doomednum | integer | `TODO-PROP-DESC` |
| sequence  | object  | `TODO-PROP-DESC` |

## Terrain

`TODO-SUMMARY`

### Properties

| Name           | Type          | Description |
|----------------|---------------|-------------|
| floorclip      | decimal       | `TODO-PROP-DESC` |
| flags          | terrain flags | `TODO-PROP-DESC` |
| addflags       | terrain flags | `TODO-PROP-DESC` |
| removeflags    | terrain flags | `TODO-PROP-DESC` |
| sound          | sound         | `TODO-PROP-DESC` |
| baseactor      | actor         | `TODO-PROP-DESC` |
| chunkactor     | actor         | `TODO-PROP-DESC` |
| chunkxvelrange | decimal       | `TODO-PROP-DESC` |
| chunkyvelrange | decimal       | `TODO-PROP-DESC` |
| chunkzvelrange | decimal       | `TODO-PROP-DESC` |
| chunkzvelbase  | decimal       | `TODO-PROP-DESC` |
| smallactor     | actor         | `TODO-PROP-DESC` |
| smallclip      | decimal       | `TODO-PROP-DESC` |
| smallsound     | sound         | `TODO-PROP-DESC` |
| damage         | integer       | `TODO-PROP-DESC` |
| damageflags    | damage flags  | `TODO-PROP-DESC` |
| damagetics     | integer       | `TODO-PROP-DESC` |
| damageseconds  | decimal       | `TODO-PROP-DESC` |
| damagesound    | sound         | `TODO-PROP-DESC` |

### Flags

`TODO-REWRITE` -- these are still a rough first pass

| Names       | Origin | Description |
|-------------|--------|-------------|
| LIQUID      | New    | terrain is a liquid; floor-bouncing missiles will remove themselves when impacting this surface. |
| LOWFRICTION | New    | terrain is low friction (i.e. ice physics) |
| ALERT       | New    | alerts monsters if a player triggers the splash |

## Behaviors

`TODO-SUMMARY`

| Name                   | Description                                     | Effects |
|------------------------|-------------------------------------------------|---------|
| player_skull           | Player gib skull                                | `TODO-BEHAVIOR-DESC` |
| player_speed_trail     | Player speed trail                              | `TODO-BEHAVIOR-DESC` |
| blaster_missile        | Dragon Claw tomed projectile                    | `TODO-BEHAVIOR-DESC` |
| blaster_ripper         | Dragon Claw tomed ripper ball                   | `TODO-BEHAVIOR-DESC` |
| skullrod_seeker        | Hellstaff tomed projectile                      | `TODO-BEHAVIOR-DESC` |
| skullrod_rain          | Hellstaff rain projectiles                      | `TODO-BEHAVIOR-DESC` |
| phoenixrod_missile     | Phoenix Rod projectile                          | `TODO-BEHAVIOR-DESC` |
| phoenixrod_flame       | Phoenix Rod tomed projectile (flamethrower)     | `TODO-BEHAVIOR-DESC` |
| mace_spawner           | Firemace spawn spot                             | `TODO-BEHAVIOR-DESC` |
| mace_deathball         | Firemace tomed projectile ("death ball")        | `TODO-BEHAVIOR-DESC` |
| key_gizmo_blue         | Blue Key Gizmo                                  | `TODO-BEHAVIOR-DESC` |
| key_gizmo_green        | Green Key Gizmo                                 | `TODO-BEHAVIOR-DESC` |
| key_gizmo_yellow       | Yellow Key Gizmo                                | `TODO-BEHAVIOR-DESC` |
| fighter_hammer_missile | Hammer of Retribution projectile                | `TODO-BEHAVIOR-DESC` |
| fighter_sword_missile  | Quietus projectile                              | `TODO-BEHAVIOR-DESC` |
| cleric_staff_missile   | Serpent Staff projectile                        | `TODO-BEHAVIOR-DESC` |
| cleric_flame_circle    | Firestorm circling flames                       | `TODO-BEHAVIOR-DESC` |
| holy_spirit            | Wraithverge spirit                              | `TODO-BEHAVIOR-DESC` |
| mage_frost_missile     | Frost Shards projectile                         | `TODO-BEHAVIOR-DESC` |
| mage_lightning_ceiling | Mage Lightning ceiling projectile               | `TODO-BEHAVIOR-DESC` |
| mage_lightning_floor   | Mage Lightning floor projectile                 | `TODO-BEHAVIOR-DESC` |
| mage_lightning_zap     | Mage Lightning vertical zap projectile          | `TODO-BEHAVIOR-DESC` |
| mage_staff_oldmissile  | Old (unused) Bloodscourge projectile            | `TODO-BEHAVIOR-DESC` |
| mage_staff_missile     | Bloodscourge projectile                         | `TODO-BEHAVIOR-DESC` |
| firebomb               | Time Bomb / Mage Flechette                      | `TODO-BEHAVIOR-DESC` |
| egg_missile            | Morph Ovum projectile                           | `TODO-BEHAVIOR-DESC` |
| pork_missile           | Porkalataor projectile                          | `TODO-BEHAVIOR-DESC` |
| telother_missile       | Banishment Device projectile                    | `TODO-BEHAVIOR-DESC` |
| telother_spot          | Banishment Device landing spot (new to Medusa)  | `TODO-BEHAVIOR-DESC` |
| mummy_missile          | Nitrogolem projectile                           | `TODO-BEHAVIOR-DESC` |
| knight_axe_green       | Undead Warrior green axe                        | `TODO-BEHAVIOR-DESC` |
| knight_axe_red         | Undead Warrior red axe                          | `TODO-BEHAVIOR-DESC` |
| ironlich               | Iron Lich                                       | `TODO-BEHAVIOR-DESC` |
| minotaur               | Maulotaur                                       | `TODO-BEHAVIOR-DESC` |
| minotaur_friend        | Friendly Maulotaur (Dark Servant)               | `TODO-BEHAVIOR-DESC` |
| minotaur_spread_missile| Maulotaur spread attack missile                 | `TODO-BEHAVIOR-DESC` |
| minotaur_floor_missile | Maulotaur floor missile                         | `TODO-BEHAVIOR-DESC` |
| dsparil_serpent_missile| D'Sparil serpent fireball                       | `TODO-BEHAVIOR-DESC` |
| dsparil_dismounted     | D'Sparil (dismounted)                           | `TODO-BEHAVIOR-DESC` |
| dsparil_missile        | D'Sparil magic missile                          | `TODO-BEHAVIOR-DESC` |
| whirlwind              | Iron Lich whirlwind projectile                  | `TODO-BEHAVIOR-DESC` |
| ettin                  | Ettin                                           | `TODO-BEHAVIOR-DESC` |
| centaur                | Centaur/Slaughtaur                              | `TODO-BEHAVIOR-DESC` |
| demon_green            | Green Chaos Serpent                             | `TODO-BEHAVIOR-DESC` |
| heresiarch             | Heresiarch                                      | `TODO-BEHAVIOR-DESC` |
| heresiarch_orbiter1    | Heresiarch orbiting ball 1                      | `TODO-BEHAVIOR-DESC` |
| heresiarch_orbiter2    | Heresiarch orbiting ball 2                      | `TODO-BEHAVIOR-DESC` |
| heresiarch_orbiter3    | Heresiarch orbiting ball 3                      | `TODO-BEHAVIOR-DESC` |
| heresiarch_bouncer     | Heresiarch bouncing projectile                  | `TODO-BEHAVIOR-DESC` |
| heresiarch_missile     | Heresiarch purple exploding missile             | `TODO-BEHAVIOR-DESC` |
| fighter_boss           | Zedek (Fighter Boss)                            | `TODO-BEHAVIOR-DESC` |
| cleric_boss            | Traductus (Cleric Boss)                         | `TODO-BEHAVIOR-DESC` |
| mage_boss              | Menelkir (Mage Boss)                            | `TODO-BEHAVIOR-DESC` |
| iceguy                 | Wendigo                                         | `TODO-BEHAVIOR-DESC` |
| iceguy_missile_shard   | Wendigo projectile shards                       | `TODO-BEHAVIOR-DESC` |
| stalker_leader         | Stalker Leader                                  | `TODO-BEHAVIOR-DESC` |
| stalker_missile        | Stalker missile                                 | `TODO-BEHAVIOR-DESC` |
| bishop                 | Dark Bishop                                     | `TODO-BEHAVIOR-DESC` |
| bishop_missile         | Dark Bishop projectile                          | `TODO-BEHAVIOR-DESC` |
| wraith_buried          | Buried Reiver                                   | `TODO-BEHAVIOR-DESC` |
| dragon_explosion       | Death Wyvern projectile explosion               | `TODO-BEHAVIOR-DESC` |
| poison_dart            | Poison dart trap projectile                     | `TODO-BEHAVIOR-DESC` |
| poison_cloud           | Poison cloud (cleric flechette / mushroom)      | `TODO-BEHAVIOR-DESC` |
| tree_destructible      | Destructible tree                               | `TODO-BEHAVIOR-DESC` |
| tree_burnable          | Burnable-only trees (XMas tree and large shrub) | `TODO-BEHAVIOR-DESC` |
| lynched_heartless      | Hexen lynched heartless corpse                  | `TODO-BEHAVIOR-DESC` |
| thrust_spike           | Hexen thrust spike traps                        | `TODO-BEHAVIOR-DESC` |
| pottery                | Hexen pottery                                   | `TODO-BEHAVIOR-DESC` |
| bell                   | Hexen bell                                      | `TODO-BEHAVIOR-DESC` |
| glass_shard            | Stained glass shard                             | `TODO-BEHAVIOR-DESC` |
| quake_focus            | Earthquake focus                                | `TODO-BEHAVIOR-DESC` |
| blast_effect           | Disc of Repulsion blast effect                  | `TODO-BEHAVIOR-DESC` |
| teleport_destination   | Teleport destination                            | `TODO-BEHAVIOR-DESC` |
| bridge                 | Bridge thing that spawns glitter objects        | `TODO-BEHAVIOR-DESC` |

## Actor Action Definitions

- **A_ESound**<a id="A_ESound" name="A_ESound"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_Explode**<a id="A_Explode" name="A_Explode"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PlaySound**<a id="A_PlaySound" name="A_PlaySound"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FaceTargetSound**<a id="A_FaceTargetSound" name="A_FaceTargetSound"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RandomJump**<a id="A_RandomJump" name="A_RandomJump"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_Die**<a id="A_Die" name="A_Die"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_Stop**<a id="A_Stop" name="A_Stop"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_SpawnObject**<a id="A_SpawnObject" name="A_SpawnObject"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MonsterProjectile**<a id="A_MonsterProjectile" name="A_MonsterProjectile"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MonsterBulletAttack**<a id="A_MonsterBulletAttack" name="A_MonsterBulletAttack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MonsterMeleeAttack**<a id="A_MonsterMeleeAttack" name="A_MonsterMeleeAttack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MonsterComboAttack**<a id="A_MonsterComboAttack" name="A_MonsterComboAttack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_SpawnMinion**<a id="A_SpawnMinion" name="A_SpawnMinion"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusDamage**<a id="A_RadiusDamage" name="A_RadiusDamage"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_NoiseAlert**<a id="A_NoiseAlert" name="A_NoiseAlert"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_HealChase**<a id="A_HealChase" name="A_HealChase"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_SeekTracer**<a id="A_SeekTracer" name="A_SeekTracer"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FindTracer**<a id="A_FindTracer" name="A_FindTracer"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ClearTracer**<a id="A_ClearTracer" name="A_ClearTracer"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MissileExplode**<a id="A_MissileExplode" name="A_MissileExplode"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MissileCountdown**<a id="A_MissileCountdown" name="A_MissileCountdown"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfHealthBelow**<a id="A_JumpIfHealthBelow" name="A_JumpIfHealthBelow"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTargetInSight**<a id="A_JumpIfTargetInSight" name="A_JumpIfTargetInSight"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTargetCloser**<a id="A_JumpIfTargetCloser" name="A_JumpIfTargetCloser"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTargetAlive**<a id="A_JumpIfTargetAlive" name="A_JumpIfTargetAlive"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTracerInSight**<a id="A_JumpIfTracerInSight" name="A_JumpIfTracerInSight"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTracerCloser**<a id="A_JumpIfTracerCloser" name="A_JumpIfTracerCloser"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfTracerAlive**<a id="A_JumpIfTracerAlive" name="A_JumpIfTracerAlive"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfHasPower**<a id="A_JumpIfHasPower" name="A_JumpIfHasPower"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfHasArtifact**<a id="A_JumpIfHasArtifact" name="A_JumpIfHasArtifact"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfHasKey**<a id="A_JumpIfHasKey" name="A_JumpIfHasKey"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfWeaponOwned**<a id="A_JumpIfWeaponOwned" name="A_JumpIfWeaponOwned"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfWeaponUsable**<a id="A_JumpIfWeaponUsable" name="A_JumpIfWeaponUsable"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_JumpIfWeaponPiece**<a id="A_JumpIfWeaponPiece" name="A_JumpIfWeaponPiece"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_DeathBallBounceEX**<a id="A_DeathBallBounceEX" name="A_DeathBallBounceEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_TempestChain**<a id="A_TempestChain" name="A_TempestChain"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_TempestSpray**<a id="A_TempestSpray" name="A_TempestSpray"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_KnightAttackEX**<a id="A_KnightAttackEX" name="A_KnightAttackEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ClinkRushEX**<a id="A_ClinkRushEX" name="A_ClinkRushEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ClinkAttackEX**<a id="A_ClinkAttackEX" name="A_ClinkAttackEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_MinotaurMeleeEX**<a id="A_MinotaurMeleeEX" name="A_MinotaurMeleeEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_TrollChargeStart**<a id="A_TrollChargeStart" name="A_TrollChargeStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_TrollCharge**<a id="A_TrollCharge" name="A_TrollCharge"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_SuperDemonAttack1**<a id="A_SuperDemonAttack1" name="A_SuperDemonAttack1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_SuperDemonAttack2**<a id="A_SuperDemonAttack2" name="A_SuperDemonAttack2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_CheckThrowBomb2**<a id="A_CheckThrowBomb2" name="A_CheckThrowBomb2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ShedShardEX**<a id="A_ShedShardEX" name="A_ShedShardEX"></a>
  - Improved Frost Shards split function. Splits the shard into a number of child shards, with mathematically correct damage distribution.
  - Args: No args.
  - Implementation:
    - This function is based on A_ShedShard, with the following changes:
      - Reset the calling shard's `damage` back to 1x its normal value (i.e. `damage = info->damage`)
      - After decrementing the creatively-named `spermcount` variable, calculate child shard `damage` as follows:
        - if `spermcount == 2`, child shard `damage = info->damage * 4`
        - if `spermcount == 1`, child shard `damage = info->damage * 3`
        - if `spermcount == 0`, child shard `damage = info->damage`
          - These numbers probably look unusual at a glance, but this ensures that the total maximum amount of damage (i.e. `17` times the shard's `info->damage` field) is accounted for during every shard 'generation'. Vanilla's method results in certain generations doing much lower damage than expected, meaning the weapon's attack power gets nerfed weirdly at certain distances.
      - When child shards are spawned, the child shard copies the `target` field from its parent before P_CheckMissileSpawn is called. This fixes the vanilla bug where you can collide with your own frost shards when running forward with Boots of Speed enbled.
      - Remove the lines that set `args[0]` on the child shards; replace it instead with the appropriate `damage` value as outlined above.
  - Notes:
    - The vanilla game's frost shard projectiles used rather convoluted logic to determine the damage of each shard, including a special-case check for `MT_SHARDFX1` in P_DamageMobj. For simplicity, this function simply sets the shard's `damage` field directly.

- **A_CentaurEXGuardRaise**<a id="A_CentaurEXGuardRaise" name="A_CentaurEXGuardRaise"></a>
  - New centaur shield behavior. Guards and reflects attacks within a limited angle, instead of a full 360 degrees.
  - Args:
    - `guardangle (angle)`: Angle to guard attacks from, relative to actor's current facing angle
    - `guardpercent (decimal)`: Damage absorption percent, from 0.0 to 1.0
  - Implementation:
    - Set the calling player actor's [`guardangle`](#guardangle) and [`guardpercent`](#guardpercent) fields to the values provided via args
    - Set the [REFLECTIVE](#MF_REFLECTIVE) flag
    - Call `A_FaceTarget`
  - Notes:
    - See the [Shield Block & Parry](#shield-block--parry) section for additional details
    - Damage is _reduced_ by a factor of `guardpercent`, e.g. a value of `0.25` will result in the guarding actor taking 75% of the attack's full damage value.

- **A_CentaurEXGuardHold**<a id="A_CentaurEXGuardHold" name="A_CentaurEXGuardHold"></a>
  - New centaur shield behavior. Decides whether to lower the shield and perform a melee attack.
  - Args:
    - `meleechance (uint)`: Chance (out of 255) to initiate a melee attack.
  - Implementation:
    - Check if `target` is within melee range
    - Check if `P_Random() < meleechance`
    - If both of the above are true, do the following:
      - Call [A_CentaurEXGuardLower](#A_CentaurEXGuardLower)
      - Jump to `meleestate`

- **A_CentaurEXGuardLower**<a id="A_CentaurEXGuardLower" name="A_CentaurEXGuardLower"></a>
  - New centaur shield behavior. Lowers the shield, ending the guard.
  - No args.
  - Implementation:
    - Remove the [REFLECTIVE](#MF_REFLECTIVE) flag
    - Zero out the [`guardangle`](#guardangle) and [`guardpercent`](#guardpercent) fields
  - Notes:
    - See the [Shield Block & Parry](#shield-block--parry) section for additional details

- **A_CentaurAttack2EX**<a id="A_CentaurAttack2EX" name="A_CentaurAttack2EX"></a>
  - Improved Slaughtaur projectile attack. If player is within melee range, attempt to melee attack the player instead.
  - Args:
    - `type (actor)`: Missile actor to spawn
    - `sound (sound)`: Sound to play on the calling actor
    - `meleerange (decimal)`: Melee attack range; if zero, use the actor's `meleerange` property instead.
    - `meleestate (state)`: Melee state to jump to; if null, use the actor's `meleestate` property instead.
  - Implementation:
    - Check if `target` is within `meleerange`; if so, jump to `meleestate` and return.
    - Otherwise, spawn a missile of `type` and play `sound` on the actor.

- **A_SerpentHumpDecideEX**<a id="A_SerpentHumpDecideEX" name="A_SerpentHumpDecideEX"></a>
  - Fixed version of A_SerpentHumpDecide. Checks if its `target` is in sight before attempting a ranged attack.
  - No args.
  - Implementation:
    - This function de-hardcodes a few things:
      - Check if the actor has a `missilestate` defined to determine if this is a Stalker Leader, instead of checking if the actor type ID is `MT_SERPENTLEADER`
      - Use `meleestate` instead of `S_SERPENT_SURFACE1`
      - Use "Hump" [`customstate`](#customstate) instead of `S_SERPENT_HUMP1`
    - Add a call to `P_CheckSight` (to determine if the actor's `target` is in sight) to each conditional that results in a state jump to `meleestate` if true.
      - This fixes the vanilla bug where Stalker Leaders will continually fire projectiles even when the player is out of sight.

- **A_BishopSpawnBlurEX**<a id="A_BishopSpawnBlurEX" name="A_BishopSpawnBlurEX"></a>
  - Fixed version of A_BishopSpawnBlur. Checks if its `target` is in sight before attempting a ranged attack.
  - No args.
  - Implementation:
    - This function de-hardcodes a few things:
      - Use `seestate` instead of `S_BISHOP_WALK1`
      - Use `missilestate` instead of `S_BISHOP_ATK1`
    - Just before the conditional that checks whether to jump to `seestate` or `missilestate`, add a call to `P_CheckSight` (to determine if the actor's `target` is in sight); if it returns `false` (or `target` is null), always jump to `seestate`.
      - This fixes the vanilla bug where Bishops will continually fire projectiles even when the player is out of sight.

- **A_FlyTarget**<a id="A_FlyTarget" name="A_FlyTarget"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FlyChase**<a id="A_FlyChase" name="A_FlyChase"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

## Weapon Action Definitions

- **A_WeaponSound**<a id="A_WeaponSound" name="A_WeaponSound"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponProjectile**<a id="A_WeaponProjectile" name="A_WeaponProjectile"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponBulletAttack**<a id="A_WeaponBulletAttack" name="A_WeaponBulletAttack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponMeleeAttack**<a id="A_WeaponMeleeAttack" name="A_WeaponMeleeAttack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJump**<a id="A_WeaponJump" name="A_WeaponJump"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ConsumeAmmo**<a id="A_ConsumeAmmo" name="A_ConsumeAmmo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_CheckAmmo**<a id="A_CheckAmmo" name="A_CheckAmmo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RefireTo**<a id="A_RefireTo" name="A_RefireTo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_GunFlashTo**<a id="A_GunFlashTo" name="A_GunFlashTo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponAlert**<a id="A_WeaponAlert" name="A_WeaponAlert"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfHasPower**<a id="A_WeaponJumpIfHasPower" name="A_WeaponJumpIfHasPower"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfHasArtifact**<a id="A_WeaponJumpIfHasArtifact" name="A_WeaponJumpIfHasArtifact"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfHasKey**<a id="A_WeaponJumpIfHasKey" name="A_WeaponJumpIfHasKey"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponOwned**<a id="A_WeaponJumpIfWeaponOwned" name="A_WeaponJumpIfWeaponOwned"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponUsable**<a id="A_WeaponJumpIfWeaponUsable" name="A_WeaponJumpIfWeaponUsable"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponPiece**<a id="A_WeaponJumpIfWeaponPiece" name="A_WeaponJumpIfWeaponPiece"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_GauntletAttackEX**<a id="A_GauntletAttackEX" name="A_GauntletAttackEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireGoldWandEX1**<a id="A_FireGoldWandEX1" name="A_FireGoldWandEX1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireCrossbowEX1**<a id="A_FireCrossbowEX1" name="A_FireCrossbowEX1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireCrossbowEX2**<a id="A_FireCrossbowEX2" name="A_FireCrossbowEX2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireSkullRodEX1**<a id="A_FireSkullRodEX1" name="A_FireSkullRodEX1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireSkullRodEX2**<a id="A_FireSkullRodEX2" name="A_FireSkullRodEX2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FirePhoenixEX2**<a id="A_FirePhoenixEX2" name="A_FirePhoenixEX2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireMaceEX1**<a id="A_FireMaceEX1" name="A_FireMaceEX1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireMaceEX2**<a id="A_FireMaceEX2" name="A_FireMaceEX2"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FireTempestWandPL1**<a id="A_FireTempestWandPL1" name="A_FireTempestWandPL1"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FAxeCheckUp**<a id="A_FAxeCheckUp" name="A_FAxeCheckUp"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FAxeCheckUpG**<a id="A_FAxeCheckUpG" name="A_FAxeCheckUpG"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FAxeCheckReady**<a id="A_FAxeCheckReady" name="A_FAxeCheckReady"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FAxeCheckReadyG**<a id="A_FAxeCheckReadyG" name="A_FAxeCheckReadyG"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_FAxeCheckAtk**<a id="A_FAxeCheckAtk" name="A_FAxeCheckAtk"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_CMaceAttackEX**<a id="A_CMaceAttackEX" name="A_CMaceAttackEX"></a>
  - Parameterized Mace of Contrition attack. Inflicts bonus damage on actors stunned by a shield parry.
  - Args:
    - `damagemin (uint)`: Minimum attack damage
    - `damagemax (uint)`: Maximum attack damage
    - `stundamagemultiplier (decimal)`: Damage multiplier to apply to actors with the [STUNNED](#MF_STUNNED) flag
    - `pufftype (puff)`: [Puff](#puff) to use
    - `range (uint)`: Range to use for checking if the attack hits an actor
    - `wallrange (uint)`: Range to use for checking if the attack hits a wall
  - Notes:
    - This function uses Hexen-style [melee targeting](#melee-targeting), with a fixed angle of 45 degrees.
    - Base attack damage is randomized between `damagemin` and `damagemax`, inclusive. This is then multiplied by `stundamagemultiplier` if the damaged actor has the [STUNNED](#MF_STUNNED) flag.
    - The vanilla A_CMaceAttack function uses different ranges for checking the distance to a wall versus an actor, hence the two separate args.

- **A_CShieldParry**<a id="A_CShieldParry" name="A_CShieldParry"></a>
  - Cleric shield parry. Stuns melee attackers and reflects projectiles.
  - Args:
    - `parrystate (state)`: State to jump to on a successful parry
    - `parrysound (sound)`: Sound to play on a successful parry
  - Implementation:
    - Set the [SHIELDPARRY](MF_SHIELDPARRY) flag on the calling player actor
    - Update the [Weapon Y Offset](#shield-block-offset) using an `offsettics` value of `4`
    - If a parry or guard occurred in the previous frame (i.e. the player's `lastguardtime` is within 1 tic of the current `leveltime`), play `parrysound` and jump to `parrystate` (if not null)
  - Notes:
    - See the [Shield Block & Parry](#shield-block--parry) section for additional details

- **A_CShieldGuard**<a id="A_CShieldGuard" name="A_CShieldGuard"></a>
  - Cleric shield block. Reduces damage from attacks within a certain angle.
  - Args:
    - `guardangle (angle)`: Angle to guard attacks from, relative to actor's current facing angle
    - `guardpercent (decimal)`: Damage absorption percent, from 0.0 to 1.0
    - `guardsound (sound)`: Sound to play on a successful guard
  - Implementation:
    - Remove the [SHIELDPARRY](MF_SHIELDPARRY) flag from the calling player actor
    - Set the calling player actor's [`guardangle`](#guardangle) and [`guardpercent`](#guardpercent) fields to the values provided via args
    - Update the [Weapon Y Offset](#shield-block-offset) using an `offsettics` value of `3`
    - If a parry or guard occurred in the previous frame (i.e. the calling player's `lastguardtime` is within 1 tic of the current `leveltime`), play `guardsound`
  - Notes:
    - See the [Shield Block & Parry](#shield-block--parry) section for additional details
    - Damage is _reduced_ by a factor of `guardpercent`, e.g. a value of `0.25` will result in the guarding actor taking 75% of the attack's full damage value.

- **A_CShieldLower**<a id="A_CShieldLower" name="A_CShieldLower"></a>
  - Ends cleric shield parry & block behavior.
  - No Args.
  - Implementation:
    - Remove the [SHIELDPARRY](MF_SHIELDPARRY) flag from the calling player 
    - Zero out the calling player actor's [`guardangle`](#guardangle) and [`guardpercent`](#guardpercent) fields
  - Notes:
    - See the [Shield Block & Parry](#shield-block--parry) section for additional details

- **A_CStaffDrainEX**<a id="A_CStaffDrainEX" name="A_CStaffDrainEX"></a>
  - Parameterized Serpent Staff health-drain attack.
  - Args:
    - `damagemin (uint)`: Minimum attack damage
    - `damagemax (uint)`: Maximum attack damage
    - `healthgainpercent (decimal)`: Percentage of damage dealt to heal the player on a successful attack
    - `meleerange (decimal)`: Attack range
    - `meleeangle (angle)`: Attack angle, for Hexen-style [melee targeting](#melee-targeting). Value represents the maximum angle from the view direction the attack will land (i.e. total FOV cone is double this value)
    - `meleestate (state)`: State to jump to if attack is successful
    - `meleesound (sound)`: Sound to play if attack is successful
  - Notes:
    - This function uses Hexen-style [melee targeting](#melee-targeting), with customizable `meleeangle`.
    - Attack damage is randomized between `damagemin` and `damagemax`, inclusive.
    - Healing amount is the final attack damage multiplied by `healthgainpercent`, rounded down to the nearest integer.
    - Healing will not increase the player's health beyond 100. This value will ideally be de-hardcoded in a future spec.
    - Melee state jump & sound will still occur even if the player is at maximum health.
    - Ammo is consumed (i.e. P_ConsumeAmmo is called) when melee attack is successful.

- **A_FireConeEX**<a id="A_FireConeEX" name="A_FireConeEX"></a>
  - Parameterized Frost Shards attack.
  - Args:
    - `type (actor)`: Shard actor to spawn
    - `sound (sound)`: Sound to play
    - `meleedamagemin (uint)`: Minimum melee attack damage
    - `meleedamagemax (uint)`: Maximum melee attack damage
  - Implementation:
    - Call P_ConsumeAmmo
    - Play `sound` on the player actor
    - Attempt to perform a melee attack; if successful, return early
    - Otherwise, spawn a player missile of `type` (i.e. via P_SpawnPlayerMissile) and do the following to it:
      - Set `special1` to `15` (i.e. all four SHARDSPAWN_ constants OR'd together)
      - Set `special2` to `3` (i.e. number of generations = 3)
      - Multiply its `damage` by `17` (i.e. the total number of shards in a single Frost Shards attack)
  - Notes:
    - This function uses Hexen-style [melee targeting](#melee-targeting), with the default MELEERANGE and a fixed angle of 45 degrees.
    - Ammo is consumed (i.e. P_ConsumeAmmo is called) when calling this function.
    - This function is designed to be used alongside [A_ShedShardEX](#A_ShedShardEX).

- **A_MStaffAttackEX**<a id="A_MStaffAttackEX" name="A_MStaffAttackEX"></a>
  - Parameterized Bloodscourge attack.
  - Args:
    - `type1 (actor)`: Type of center missile
    - `type2 (actor)`: Type of side missiles (two will be spawned)
    - `angle (angle)`: Angle to fire side missiles at
    - `sound (sound)`: Sound to play
    - `usepalette (bool)`: If true, apply the Bloodscourge's special palette flash effect
  - Notes: 
    - Ammo is consumed (i.e. P_ConsumeAmmo is called) when calling this function.

## Playerclass Action Definitions

- **A_InvulnStandardStart**<a id="A_InvulnStandardStart" name="A_InvulnStandardStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnStandardEnd**<a id="A_InvulnStandardEnd" name="A_InvulnStandardEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnPhasedTick**<a id="A_InvulnPhasedTick" name="A_InvulnPhasedTick"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnPhasedDraw**<a id="A_InvulnPhasedDraw" name="A_InvulnPhasedDraw"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnPhasedEnd**<a id="A_InvulnPhasedEnd" name="A_InvulnPhasedEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnReflectiveStart**<a id="A_InvulnReflectiveStart" name="A_InvulnReflectiveStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_InvulnReflectiveEnd**<a id="A_InvulnReflectiveEnd" name="A_InvulnReflectiveEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

## Pickup Action Definitions

- **A_PickupHealth**<a id="A_PickupHealth" name="A_PickupHealth"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupBasicArmor**<a id="A_PickupBasicArmor" name="A_PickupBasicArmor"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupHexenArmor**<a id="A_PickupHexenArmor" name="A_PickupHexenArmor"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupBackpack**<a id="A_PickupBackpack" name="A_PickupBackpack"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupWeapon**<a id="A_PickupWeapon" name="A_PickupWeapon"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupWeaponPiece**<a id="A_PickupWeaponPiece" name="A_PickupWeaponPiece"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupAmmo**<a id="A_PickupAmmo" name="A_PickupAmmo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupDualAmmo**<a id="A_PickupDualAmmo" name="A_PickupDualAmmo"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupPower**<a id="A_PickupPower" name="A_PickupPower"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupArtifact**<a id="A_PickupArtifact" name="A_PickupArtifact"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PickupKey**<a id="A_PickupKey" name="A_PickupKey"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

## Power Action Definitions

- **A_PowerInvulnerableStart**<a id="A_PowerInvulnerableStart" name="A_PowerInvulnerableStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerInvulnerableTick**<a id="A_PowerInvulnerableTick" name="A_PowerInvulnerableTick"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerInvulnerableEnd**<a id="A_PowerInvulnerableEnd" name="A_PowerInvulnerableEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerInvisibilityStart**<a id="A_PowerInvisibilityStart" name="A_PowerInvisibilityStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerInvisibilityEnd**<a id="A_PowerInvisibilityEnd" name="A_PowerInvisibilityEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerStrengthStart**<a id="A_PowerStrengthStart" name="A_PowerStrengthStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerFlightStart**<a id="A_PowerFlightStart" name="A_PowerFlightStart"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerFlightEnd**<a id="A_PowerFlightEnd" name="A_PowerFlightEnd"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerWeaponLevel2Start**<a id="A_PowerWeaponLevel2Start" name="A_PowerWeaponLevel2Start"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_PowerWeaponLevel2End**<a id="A_PowerWeaponLevel2End" name="A_PowerWeaponLevel2End"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

## Artifact Action Definitions

- **A_ArtiGivePower**<a id="A_ArtiGivePower" name="A_ArtiGivePower"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiHealth**<a id="A_ArtiHealth" name="A_ArtiHealth"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiEgg**<a id="A_ArtiEgg" name="A_ArtiEgg"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiSummon**<a id="A_ArtiSummon" name="A_ArtiSummon"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiTeleport**<a id="A_ArtiTeleport" name="A_ArtiTeleport"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiTeleportOther**<a id="A_ArtiTeleportOther" name="A_ArtiTeleportOther"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiBoostMana**<a id="A_ArtiBoostMana" name="A_ArtiBoostMana"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiBoostArmor**<a id="A_ArtiBoostArmor" name="A_ArtiBoostArmor"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiBlastRadius**<a id="A_ArtiBlastRadius" name="A_ArtiBlastRadius"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiFlechette**<a id="A_ArtiFlechette" name="A_ArtiFlechette"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiThrowingBomb**<a id="A_ArtiThrowingBomb" name="A_ArtiThrowingBomb"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiThrowingBombEX**<a id="A_ArtiThrowingBombEX" name="A_ArtiThrowingBombEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiPoisonBag**<a id="A_ArtiPoisonBag" name="A_ArtiPoisonBag"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiFireBomb**<a id="A_ArtiFireBomb" name="A_ArtiFireBomb"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiHealingRadius**<a id="A_ArtiHealingRadius" name="A_ArtiHealingRadius"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusGiveArmor**<a id="A_RadiusGiveArmor" name="A_RadiusGiveArmor"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusGiveArmorEX**<a id="A_RadiusGiveArmorEX" name="A_RadiusGiveArmorEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusGiveHealth**<a id="A_RadiusGiveHealth" name="A_RadiusGiveHealth"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusGiveMana**<a id="A_RadiusGiveMana" name="A_RadiusGiveMana"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_RadiusGiveManaEX**<a id="A_RadiusGiveManaEX" name="A_RadiusGiveManaEX"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:

- **A_ArtiPuzzleItem**<a id="A_ArtiPuzzleItem" name="A_ArtiPuzzleItem"></a>
  - `TODO-ACTION-DEF`
  - Args:
  - Notes:
