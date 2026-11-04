# Raven EX Specification

## Actors

Also known as 'things' in DEHACKED, 'mobjs' in the source code, and 'entities' in virtually every other game engine.

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

### Actions - Base

Actions (a.k.a. 'codepointers') from the base vanilla games. Unless otherwise mentioned, these functions behave identically to their vanilla counterparts.

| Name                       | Origin             | Description |
|----------------------------|--------------------|-------------|
| A_Look                     | Common             | |
| A_Chase                    | Common             | |
| A_FaceTarget               | Common             | |
| A_Scream                   | Common             | |
| A_Pain                     | Common             | |
| A_FreeTargMobj             | Common             | |
| A_RestoreSpecialThing1     | Common             | |
| A_RestoreSpecialThing2     | Common             | |
| A_RestoreArtifact          | Common             | |
| A_HideThing                | Common             | |
| A_UnHideThing              | Common             | |
| A_NoBlocking               | Common             | |
| A_AddPlayerCorpse          | Common             | |
| A_SkullPop                 | Common             | |
| A_CheckBurnGone            | Common             | |
| A_CheckSkullFloor          | Common             | |
| A_CheckSkullDone           | Common             | |
| [A_ESound](#A_ESound)      | Common - Modified  | Now plays the actor's 'activesound', instead of doing a hardcoded branch based on mobj type. |
| A_PlayerPain               | Heretic            | |
| A_PlayerScream             | Heretic            | |
| A_BossDeath                | Heretic            | |
| [A_Explode](#A_Explode)    | Heretic - Modified | Branches based on actor's behavior field, instead of its mobjtype |
| A_PodPain                  | Heretic            | |
| A_RemovePod                | Heretic            | |
| A_MakePod                  | Heretic            | |
| A_InitKeyGizmo             | Heretic            | |
| A_VolcanoSet               | Heretic            | |
| A_VolcanoBlast             | Heretic            | |
| A_BeastPuff                | Heretic            | |
| A_VolcBallImpact           | Heretic            | |
| A_SpawnTeleGlitter         | Heretic            | |
| A_SpawnTeleGlitter2        | Heretic            | |
| A_AccTeleGlitter           | Heretic            | |
| A_SpawnRippers             | Heretic            | |
| A_MacePL1Check             | Heretic            | |
| A_MaceBallImpact           | Heretic            | |
| A_MaceBallImpact2          | Heretic            | |
| A_DeathBallImpact          | Heretic            | |
| A_SkullRodPL2Seek          | Heretic            | |
| A_AddPlayerRain            | Heretic            | |
| A_HideInCeiling            | Heretic            | |
| A_SkullRodStorm            | Heretic            | |
| A_RainImpact               | Heretic            | |
| A_PhoenixPuff              | Heretic            | |
| A_FlameEnd                 | Heretic            | |
| A_FloatPuff                | Heretic            | |
| A_BoltSpark                | Heretic            | |
| A_FlameSnd                 | Heretic            | |
| A_Feathers                 | Heretic            | |
| A_ChicLook                 | Heretic            | |
| A_ChicChase                | Heretic            | |
| A_ChicPain                 | Heretic            | |
| A_ChicAttack               | Heretic            | |
| A_MummyAttack              | Heretic            | |
| A_MummyAttack2             | Heretic            | |
| A_MummySoul                | Heretic            | |
| A_ContMobjSound            | Heretic            | |
| A_MummyFX1Seek             | Heretic            | |
| A_BeastAttack              | Heretic            | |
| A_SnakeAttack              | Heretic            | |
| A_SnakeAttack2             | Heretic            | |
| A_LichAttack               | Heretic            | Named A_HeadAttack in vanilla; renamed to avoid ambiguity with Cacodemon attack function (matches GZDoom's naming) |
| A_HeadIceImpact            | Heretic            | |
| A_HeadFireGrow             | Heretic            | |
| A_WhirlwindSeek            | Heretic            | |
| A_ClinkAttack              | Heretic            | |
| A_WizAtk1                  | Heretic            | |
| A_WizAtk2                  | Heretic            | |
| A_WizAtk3                  | Heretic            | |
| A_GhostOff                 | Heretic            | |
| A_ImpMeAttack              | Heretic            | |
| A_ImpMsAttack              | Heretic            | |
| A_ImpMsAttack2             | Heretic            | |
| A_ImpDeath                 | Heretic            | |
| A_ImpXDeath1               | Heretic            | |
| A_ImpXDeath2               | Heretic            | |
| A_ImpExplode               | Heretic            | |
| A_KnightAttack             | Heretic            | |
| A_DripBlood                | Heretic            | |
| A_Sor1Chase                | Heretic            | |
| A_Sor1Pain                 | Heretic            | |
| A_Srcr1Attack              | Heretic            | |
| A_SorZap                   | Heretic            | |
| A_SorcererRise             | Heretic            | |
| A_SorRise                  | Heretic            | |
| A_SorSightSnd              | Heretic            | |
| A_Srcr2Decide              | Heretic            | |
| A_Srcr2Attack              | Heretic            | |
| A_Sor2DthInit              | Heretic            | |
| A_SorDSph                  | Heretic            | |
| A_Sor2DthLoop              | Heretic            | |
| A_SorDExp                  | Heretic            | |
| A_SorDBon                  | Heretic            | |
| A_BlueSpark                | Heretic            | |
| A_GenWizard                | Heretic            | |
| A_MinotaurAtk1             | Heretic            | |
| A_MinotaurDecide           | Heretic            | |
| A_MinotaurAtk2             | Heretic            | |
| A_MinotaurAtk3             | Heretic            | |
| A_MinotaurCharge           | Heretic            | |
| A_MntrFloorFire            | Heretic            | |
| A_FlameCheck               | Hexen              | |
| A_Summon                   | Hexen              | |
| A_ThrustInitUp             | Hexen              | |
| A_ThrustInitDn             | Hexen              | |
| A_ThrustRaise              | Hexen              | |
| A_ThrustBlock              | Hexen              | |
| A_ThrustImpale             | Hexen              | |
| A_ThrustLower              | Hexen              | |
| A_TeloSpawnC               | Hexen              | |
| A_TeloSpawnB               | Hexen              | |
| A_TeloSpawnA               | Hexen              | |
| A_TeloSpawnD               | Hexen              | |
| A_CheckTeleRing            | Hexen              | |
| A_FogSpawn                 | Hexen              | |
| A_FogMove                  | Hexen              | |
| A_Quake                    | Hexen              | |
| A_PoisonBagInit            | Hexen              | |
| A_PoisonBagDamage          | Hexen              | |
| A_PoisonBagCheck           | Hexen              | |
| A_CheckThrowBomb           | Hexen              | |
| A_NoGravity                | Hexen              | |
| A_PotteryExplode           | Hexen              | |
| A_PotteryChooseBit         | Hexen              | |
| A_PotteryCheck             | Hexen              | |
| A_CorpseBloodDrip          | Hexen              | |
| A_CorpseExplode            | Hexen              | |
| A_LeafSpawn                | Hexen              | |
| A_LeafThrust               | Hexen              | |
| A_LeafCheck                | Hexen              | |
| A_BridgeInit               | Hexen              | |
| A_BridgeOrbit              | Hexen              | |
| A_TreeDeath                | Hexen              | |
| A_PoisonShroom             | Hexen              | |
| A_SoAExplode               | Hexen              | |
| A_BellReset1               | Hexen              | |
| A_BellReset2               | Hexen              | |
| A_FSwordFlames             | Hexen              | |
| A_CStaffMissileSlither     | Hexen              | |
| A_CFlameRotate             | Hexen              | |
| A_CFlamePuff               | Hexen              | |
| A_CFlameMissile            | Hexen              | |
| A_CHolySeek                | Hexen              | |
| A_CHolyCheckScream         | Hexen              | |
| A_CHolyTail                | Hexen              | |
| A_CHolySpawnPuff           | Hexen              | |
| A_CHolyAttack2             | Hexen              | |
| A_LightningZap             | Hexen              | |
| A_LightningClip            | Hexen              | |
| A_LightningRemove          | Hexen              | |
| A_LastZap                  | Hexen              | |
| A_ZapMimic                 | Hexen              | |
| A_MStaffWeave              | Hexen              | |
| A_MStaffTrack              | Hexen              | |
| A_SnoutAttack              | Hexen              | |
| A_ShedShard                | Hexen              | |
| A_FreezeDeath              | Hexen              | |
| A_FreezeDeathChunks        | Hexen              | |
| A_SpeedFade                | Hexen              | |
| A_IceSetTics               | Hexen              | |
| A_IceCheckHeadDone         | Hexen              | |
| A_PigPain                  | Hexen              | |
| A_PigLook                  | Hexen              | |
| A_PigChase                 | Hexen              | |
| A_PigAttack                | Hexen              | |
| A_QueueCorpse              | Hexen              | |
| A_CentaurAttack            | Hexen              | |
| A_CentaurAttack2           | Hexen              | |
| A_SetReflective            | Hexen              | |
| A_CentaurDefend            | Hexen              | |
| A_UnSetReflective          | Hexen              | |
| A_CentaurDropStuff         | Hexen              | |
| A_CheckFloor               | Hexen              | |
| A_DemonAttack1             | Hexen              | |
| A_DemonAttack2             | Hexen              | |
| A_DemonDeath               | Hexen              | |
| A_Demon2Death              | Hexen              | |
| A_WraithRaiseInit          | Hexen              | |
| A_WraithRaise              | Hexen              | |
| A_WraithInit               | Hexen              | |
| A_WraithLook               | Hexen              | |
| A_WraithChase              | Hexen              | |
| A_WraithFX3                | Hexen              | |
| A_WraithMelee              | Hexen              | |
| A_WraithMissile            | Hexen              | |
| A_WraithFX2                | Hexen              | |
| A_MinotaurFade1            | Hexen              | |
| A_MinotaurFade2            | Hexen              | |
| A_MinotaurLook             | Hexen              | |
| A_MinotaurChase            | Hexen              | |
| A_MinotaurRoam             | Hexen              | |
| A_MinotaurAtk1             | Hexen              | |
| A_MinotaurDecide           | Hexen              | |
| A_MinotaurAtk2             | Hexen              | |
| A_MinotaurAtk3             | Hexen              | |
| A_MinotaurCharge           | Hexen              | |
| A_SmokePuffExit            | Hexen              | |
| A_MinotaurFade0            | Hexen              | |
| A_MntrFloorFire            | Hexen              | |
| A_SerpentChase             | Hexen              | |
| A_SerpentHumpDecide        | Hexen              | |
| A_SerpentUnHide            | Hexen              | |
| A_SerpentRaiseHump         | Hexen              | |
| A_SerpentLowerHump         | Hexen              | |
| A_SerpentHide              | Hexen              | |
| A_SerpentBirthScream       | Hexen              | |
| A_SetShootable             | Hexen              | |
| A_SerpentCheckForAttack    | Hexen              | |
| A_UnSetShootable           | Hexen              | |
| A_SerpentDiveSound         | Hexen              | |
| A_SerpentWalk              | Hexen              | |
| A_SerpentChooseAttack      | Hexen              | |
| A_SerpentMeleeAttack       | Hexen              | |
| A_SerpentMissileAttack     | Hexen              | |
| A_SerpentHeadPop           | Hexen              | |
| A_SerpentSpawnGibs         | Hexen              | |
| A_SerpentHeadCheck         | Hexen              | |
| A_FloatGib                 | Hexen              | |
| A_DelayGib                 | Hexen              | |
| A_SinkGib                  | Hexen              | |
| A_BishopDecide             | Hexen              | |
| A_BishopDoBlur             | Hexen              | |
| A_BishopSpawnBlur          | Hexen              | |
| A_BishopChase              | Hexen              | |
| A_BishopAttack             | Hexen              | |
| A_BishopAttack2            | Hexen              | |
| A_BishopPainBlur           | Hexen              | |
| A_BishopPuff               | Hexen              | |
| A_SetAltShadow             | Hexen              | |
| A_BishopMissileWeave       | Hexen              | |
| A_BishopMissileSeek        | Hexen              | |
| A_DragonInitFlight         | Hexen              | |
| A_DragonFlap               | Hexen              | |
| A_DragonFlight             | Hexen              | |
| A_DragonAttack             | Hexen              | |
| A_DragonPain               | Hexen              | |
| A_DragonCheckCrash         | Hexen              | |
| A_DragonFX2                | Hexen              | |
| A_EttinAttack              | Hexen              | |
| A_DropMace                 | Hexen              | |
| A_FiredRocks               | Hexen              | |
| A_UnSetInvulnerable        | Hexen              | |
| A_FiredChase               | Hexen              | |
| A_FiredAttack              | Hexen              | |
| A_FiredSplotch             | Hexen              | |
| A_SmBounce                 | Hexen              | |
| A_IceGuyLook               | Hexen              | |
| A_IceGuyChase              | Hexen              | |
| A_IceGuyAttack             | Hexen              | |
| A_IceGuyDie                | Hexen              | |
| A_IceGuyMissilePuff        | Hexen              | |
| A_IceGuyMissileExplode     | Hexen              | |
| A_ClassBossHealth          | Hexen              | |
| A_FastChase                | Hexen              | |
| A_FighterAttack            | Hexen              | |
| A_ClericAttack             | Hexen              | |
| A_MageAttack               | Hexen              | |
| A_SorcSpinBalls            | Hexen              | |
| A_SpeedBalls               | Hexen              | |
| A_SpawnFizzle              | Hexen              | |
| A_SorcBossAttack           | Hexen              | |
| A_SorcBallOrbit            | Hexen              | |
| A_SorcBallPop              | Hexen              | |
| A_BounceCheck              | Hexen              | |
| A_SorcFX1Seek              | Hexen              | |
| A_SorcFX2Split             | Hexen              | |
| A_SorcFX2Orbit             | Hexen              | |
| A_SorcererBishopEntry      | Hexen              | |
| A_SpawnBishop              | Hexen              | |
| A_SorcFX4Check             | Hexen              | |
| A_KoraxStep2               | Hexen              | |
| A_KoraxChase               | Hexen              | |
| A_KoraxStep                | Hexen              | |
| A_KoraxDecide              | Hexen              | |
| A_KoraxMissile             | Hexen              | |
| A_KoraxCommand             | Hexen              | |
| A_KoraxBonePop             | Hexen              | |
| A_KSpiritRoam              | Hexen              | |
| A_KBoltRaise               | Hexen              | |
| A_KBolt                    | Hexen              | |
| A_BatSpawnInit             | Hexen              | |
| A_BatSpawn                 | Hexen              | |
| A_BatMove                  | Hexen              | |


### Actions - Extended

Brand-new actions defined by Raven EX.

| Name                                           | Origin | Description |
|------------------------------------------------|--------|-------------|
| [A_PlaySound](#A_PlaySound)                    | MBF    | |
| [A_FaceTargetSound](#A_FaceTargetSound)        | New    | |
| [A_RandomJump](#A_RandomJump)                  | MBF    | |
| [A_Die](#A_Die)                                | MBF    | |
| [A_Stop](#A_Stop)                              | MBF    | |
| [A_SpawnObject](#A_SpawnObject)                | MBF21  | |
| [A_MonsterProjectile](#A_MonsterProjectile)    | MBF21  | |
| [A_MonsterBulletAttack](#A_MonsterBulletAttack)| MBF21  | |
| [A_MonsterMeleeAttack](#A_MonsterMeleeAttack)  | MBF21  | |
| [A_MonsterComboAttack](#A_MonsterComboAttack)  | New    | |
| [A_SpawnMinion](#A_SpawnMinion)                | New    | |
| [A_RadiusDamage](#A_RadiusDamage)              | MBF21  | |
| [A_NoiseAlert](#A_NoiseAlert)                  | MBF21  | |
| [A_HealChase](#A_HealChase)                    | MBF21  | |
| [A_SeekTracer](#A_SeekTracer)                  | MBF21  | |
| [A_FindTracer](#A_FindTracer)                  | MBF21  | |
| [A_ClearTracer](#A_ClearTracer)                | MBF21  | |
| [A_MissileExplode](#A_MissileExplode)          | New    | |
| [A_MissileCountdown](#A_MissileCountdown)      | New    | |
| [A_JumpIfHealthBelow](#A_JumpIfHealthBelow)    | MBF21  | |
| [A_JumpIfTargetInSight](#A_JumpIfTargetInSight)| MBF21  | |
| [A_JumpIfTargetCloser](#A_JumpIfTargetCloser)  | MBF21  | |
| [A_JumpIfTargetAlive](#A_JumpIfTargetAlive)    | New    | |
| [A_JumpIfTracerInSight](#A_JumpIfTracerInSight)| MBF21  | |
| [A_JumpIfTracerCloser](#A_JumpIfTracerCloser)  | MBF21  | |
| [A_JumpIfTracerAlive](#A_JumpIfTracerAlive)    | New    | |
| [A_JumpIfHasPower](#A_JumpIfHasPower)          | New    | |
| [A_JumpIfHasArtifact](#A_JumpIfHasArtifact)    | New    | |
| [A_JumpIfHasKey](#A_JumpIfHasKey)              | New    | |
| [A_JumpIfWeaponOwned](#A_JumpIfWeaponOwned)    | New    | |
| [A_JumpIfWeaponUsable](#A_JumpIfWeaponUsable)  | New    | |
| [A_JumpIfWeaponPiece](#A_JumpIfWeaponPiece)    | New    | |
| [A_DeathBallBounceEX](#A_DeathBallBounceEX)    | New    | |
| [A_TempestChain](#A_TempestChain)              | New    | |
| [A_TempestSpray](#A_TempestSpray)              | New    | |
| [A_KnightAttackEX](#A_KnightAttackEX)          | New    | |
| [A_ClinkRushEX](#A_ClinkRushEX)                | New    | |
| [A_ClinkAttackEX](#A_ClinkAttackEX)            | New    | |
| [A_MinotaurMeleeEX](#A_MinotaurMeleeEX)        | New    | |
| [A_TrollChargeStart](#A_TrollChargeStart)      | New    | |
| [A_TrollCharge](#A_TrollCharge)                | New    | |
| [A_SuperDemonAttack1](#A_SuperDemonAttack1)    | New    | |
| [A_SuperDemonAttack2](#A_SuperDemonAttack2)    | New    | |
| [A_CheckThrowBomb2](#A_CheckThrowBomb2)        | New    | |
| [A_ShedShardEX](#A_ShedShardEX)                | New    | |
| [A_CentaurEXGuardRaise](#A_CentaurEXGuardRaise)| New    | |
| [A_CentaurEXGuardHold](#A_CentaurEXGuardHold)  | New    | |
| [A_CentaurEXGuardLower](#A_CentaurEXGuardLower)| New    | |
| [A_CentaurAttack2EX](#A_CentaurAttack2EX)      | New    | |
| [A_SerpentHumpDecideEX](#A_SerpentHumpDecideEX)| New    | |
| [A_BishopSpawnBlurEX](#A_BishopSpawnBlurEX)    | New    | |
| [A_FlyTarget](#A_FlyTarget)                    | New    | Same behavior as GZDoom's A_FlySearch; name taken from original source code |
| [A_FlyChase](#A_FlyChase)                      | New    | Same behavior as GZDoom's A_FlyBuzz; name taken from original source code |

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

### Actions

| Name                                               | Origin | Description |
|----------------------------------------------------|--------|-------------|
| [A_InvulnStandardStart](#A_InvulnStandardStart)    | New    | |
| [A_InvulnStandardEnd](#A_InvulnStandardEnd)        | New    | |
| [A_InvulnPhasedTick](#A_InvulnPhasedTick)          | New    | |
| [A_InvulnPhasedDraw](#A_InvulnPhasedDraw)          | New    | PlayerVis |
| [A_InvulnPhasedEnd](#A_InvulnPhasedEnd)            | New    | |
| [A_InvulnReflectiveStart](#A_InvulnReflectiveStart)| New    | |
| [A_InvulnReflectiveEnd](#A_InvulnReflectiveEnd)    | New    | |

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

### Actions - Base

Actions (a.k.a. 'codepointers') from the base vanilla games. Unless otherwise mentioned, these functions behave identically to their vanilla counterparts.

| Name                                   | Origin            | Description |
|----------------------------------------|-------------------|-|
| A_Light0                               | Common            | |
| A_Light1                               | Common            | |
| A_Light2                               | Common            | |
| A_WeaponReady                          | Common            | |
| A_Lower                                | Common            | |
| A_Raise                                | Common            | |
| A_ReFire                               | Common            | |
| A_StaffAttackPL1                       | Heretic           | |
| A_StaffAttackPL2                       | Heretic           | |
| A_BeakReady                            | Heretic           | |
| A_BeakRaise                            | Heretic           | |
| A_BeakAttackPL1                        | Heretic           | |
| A_BeakAttackPL2                        | Heretic           | |
| A_GauntletAttack                       | Heretic           | |
| A_FireBlasterPL1                       | Heretic           | |
| A_FireBlasterPL2                       | Heretic           | |
| A_FireMacePL1                          | Heretic           | |
| A_FireMacePL2                          | Heretic           | |
| A_FireSkullRodPL1                      | Heretic           | |
| A_FireSkullRodPL2                      | Heretic           | |
| A_FireGoldWandPL1                      | Heretic           | |
| A_FireGoldWandPL2                      | Heretic           | |
| A_FirePhoenixPL1                       | Heretic           | |
| A_InitPhoenixPL2                       | Heretic           | |
| A_FirePhoenixPL2                       | Heretic           | |
| A_ShutdownPhoenixPL2                   | Heretic           | |
| A_FireCrossbowPL1                      | Heretic           | |
| A_FireCrossbowPL2                      | Heretic           | |
| A_FPunchAttack                         | Hexen             | |
| A_FAxeAttack                           | Hexen             | |
| A_SnoutAttack                          | Hexen             | |
| A_FHammerAttack                        | Hexen             | |
| A_FHammerThrow                         | Hexen             | |
| A_FSwordAttack                         | Hexen             | |
| A_CMaceAttack                          | Hexen             | |
| A_CStaffInitBlink                      | Hexen             | |
| A_CStaffCheckBlink                     | Hexen             | |
| A_CStaffCheck                          | Hexen             | |
| A_CStaffAttack                         | Hexen             | |
| A_CFlameAttack                         | Hexen             | |
| A_CHolyAttack                          | Hexen             | |
| A_CHolyPalette                         | Hexen             | |
| A_MWandAttack                          | Hexen             | |
| A_LightningReady                       | Hexen             | |
| A_MLightningAttack                     | Hexen             | |
| A_MStaffAttack                         | Hexen             | |
| A_MStaffPalette                        | Hexen             | |
| A_FireConePL1                          | Hexen             | |

### Actions - Extended

Brand-new actions defined by Raven EX.

| Name                                                     | Origin | Description |
|----------------------------------------------------------|--------|-|
| [A_WeaponSound](#A_WeaponSound)                          | MBF21  | |
| [A_WeaponProjectile](#A_WeaponProjectile)                | MBF21  | |
| [A_WeaponBulletAttack](#A_WeaponBulletAttack)            | MBF21  | |
| [A_WeaponMeleeAttack](#A_WeaponMeleeAttack)              | MBF21  | |
| [A_WeaponJump](#A_WeaponJump)                            | MBF21  | |
| [A_ConsumeAmmo](#A_ConsumeAmmo)                          | MBF21  | |
| [A_CheckAmmo](#A_CheckAmmo)                              | MBF21  | |
| [A_RefireTo](#A_RefireTo)                                | MBF21  | |
| [A_GunFlashTo](#A_GunFlashTo)                            | MBF21  | |
| [A_WeaponAlert](#A_WeaponAlert)                          | MBF21  | |
| [A_WeaponJumpIfHasPower](#A_WeaponJumpIfHasPower)        | New    | |
| [A_WeaponJumpIfHasArtifact](#A_WeaponJumpIfHasArtifact)  | New    | |
| [A_WeaponJumpIfHasKey](#A_WeaponJumpIfHasKey)            | New    | |
| [A_WeaponJumpIfWeaponOwned](#A_WeaponJumpIfWeaponOwned)  | New    | |
| [A_WeaponJumpIfWeaponUsable](#A_WeaponJumpIfWeaponUsable)| New    | |
| [A_WeaponJumpIfWeaponPiece](#A_WeaponJumpIfWeaponPiece)  | New    | |
| [A_GauntletAttackEX](#A_GauntletAttackEX)                | New    | |
| [A_FireGoldWandEX1](#A_FireGoldWandEX1)                  | New    | |
| [A_FireCrossbowEX1](#A_FireCrossbowEX1)                  | New    | |
| [A_FireCrossbowEX2](#A_FireCrossbowEX2)                  | New    | |
| [A_FireSkullRodEX1](#A_FireSkullRodEX1)                  | New    | |
| [A_FireSkullRodEX2](#A_FireSkullRodEX2)                  | New    | |
| [A_FirePhoenixEX2](#A_FirePhoenixEX2)                    | New    | |
| [A_FireMaceEX1](#A_FireMaceEX1)                          | New    | |
| [A_FireMaceEX2](#A_FireMaceEX2)                          | New    | |
| [A_FireTempestWandPL1](#A_FireTempestWandPL1)            | New    | |
| [A_FAxeCheckUp](#A_FAxeCheckUp)                          | New    | Same behavior as GZDoom's function of the same name. |
| [A_FAxeCheckUpG](#A_FAxeCheckUpG)                        | New    | Same behavior as GZDoom's function of the same name. |
| [A_FAxeCheckReady](#A_FAxeCheckReady)                    | New    | Same behavior as GZDoom's function of the same name. |
| [A_FAxeCheckReadyG](#A_FAxeCheckReadyG)                  | New    | Same behavior as GZDoom's function of the same name. |
| [A_FAxeCheckAtk](#A_FAxeCheckAtk)                        | New    | Same behavior as GZDoom's function of the same name. |
| [A_CMaceAttackEX](#A_CMaceAttackEX)                      | New    | |
| [A_CShieldParry](#A_CShieldParry)                        | New    | |
| [A_CShieldGuard](#A_CShieldGuard)                        | New    | |
| [A_CShieldLower](#A_CShieldLower)                        | New    | |
| [A_CStaffDrainEX](#A_CStaffDrainEX)                      | New    | |
| [A_FireConeEX](#A_FireConeEX)                            | New    | |
| [A_MStaffAttackEX](#A_MStaffAttackEX)                    | New    | |

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

### Actions

| Name                                       | Origin | Description |
|--------------------------------------------|--------|-------------|
| [A_PickupHealth](#A_PickupHealth)          | New    | |
| [A_PickupBasicArmor](#A_PickupBasicArmor)  | New    | |
| [A_PickupHexenArmor](#A_PickupHexenArmor)  | New    | |
| [A_PickupBackpack](#A_PickupBackpack)      | New    | |
| [A_PickupWeapon](#A_PickupWeapon)          | New    | |
| [A_PickupWeaponPiece](#A_PickupWeaponPiece)| New    | |
| [A_PickupAmmo](#A_PickupAmmo)              | New    | |
| [A_PickupDualAmmo](#A_PickupDualAmmo)      | New    | |
| [A_PickupPower](#A_PickupPower)            | New    | |
| [A_PickupArtifact](#A_PickupArtifact)      | New    | |
| [A_PickupKey](#A_PickupKey)                | New    | |

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

### Actions

| Name                                                 | Origin | Description |
|------------------------------------------------------|--------|-------------|
| [A_PowerInvulnerableStart](#A_PowerInvulnerableStart)| New    | |
| [A_PowerInvulnerableTick](#A_PowerInvulnerableTick)  | New    | |
| [A_PowerInvulnerableEnd](#A_PowerInvulnerableEnd)    | New    | |
| [A_PowerInvisibilityStart](#A_PowerInvisibilityStart)| New    | |
| [A_PowerInvisibilityEnd](#A_PowerInvisibilityEnd)    | New    | |
| [A_PowerStrengthStart](#A_PowerStrengthStart)        | New    | |
| [A_PowerFlightStart](#A_PowerFlightStart)            | New    | |
| [A_PowerFlightEnd](#A_PowerFlightEnd)                | New    | |
| [A_PowerWeaponLevel2Start](#A_PowerWeaponLevel2Start)| New    | |
| [A_PowerWeaponLevel2End](#A_PowerWeaponLevel2End)    | New    | |

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

### Actions

| Name                                         | Origin | Description |
|----------------------------------------------|--------|-------------|
| [A_ArtiGivePower](#A_ArtiGivePower)          | New    | |
| [A_ArtiHealth](#A_ArtiHealth)                | New    | |
| [A_ArtiEgg](#A_ArtiEgg)                      | New    | |
| [A_ArtiSummon](#A_ArtiSummon)                | New    | |
| [A_ArtiTeleport](#A_ArtiTeleport)            | New    | |
| [A_ArtiTeleportOther](#A_ArtiTeleportOther)  | New    | |
| [A_ArtiBoostMana](#A_ArtiBoostMana)          | New    | |
| [A_ArtiBoostArmor](#A_ArtiBoostArmor)        | New    | |
| [A_ArtiBlastRadius](#A_ArtiBlastRadius)      | New    | |
| [A_ArtiFlechette](#A_ArtiFlechette)          | New    | |
| [A_ArtiThrowingBomb](#A_ArtiThrowingBomb)    | New    | |
| [A_ArtiThrowingBombEX](#A_ArtiThrowingBombEX)| New    | |
| [A_ArtiPoisonBag](#A_ArtiPoisonBag)          | New    | |
| [A_ArtiFireBomb](#A_ArtiFireBomb)            | New    | |
| [A_ArtiHealingRadius](#A_ArtiHealingRadius)  | New    | |
| [A_RadiusGiveArmor](#A_RadiusGiveArmor)      | New    | |
| [A_RadiusGiveArmorEX](#A_RadiusGiveArmorEX)  | New    | |
| [A_RadiusGiveHealth](#A_RadiusGiveHealth)    | New    | |
| [A_RadiusGiveMana](#A_RadiusGiveMana)        | New    | |
| [A_RadiusGiveManaEX](#A_RadiusGiveManaEX)    | New    | |
| [A_ArtiPuzzleItem](#A_ArtiPuzzleItem)        | New    | |

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

## Actor Action Definitions

- **A_ESound**<a id="A_ESound"></a>
  - Description:
  - Args:
  - Notes:

- **A_Explode**<a id="A_Explode"></a>
  - Description:
  - Args:
  - Notes:

- **A_PlaySound**<a id="A_PlaySound"></a>
  - Description:
  - Args:
  - Notes:

- **A_FaceTargetSound**<a id="A_FaceTargetSound"></a>
  - Description:
  - Args:
  - Notes:

- **A_RandomJump**<a id="A_RandomJump"></a>
  - Description:
  - Args:
  - Notes:

- **A_Die**<a id="A_Die"></a>
  - Description:
  - Args:
  - Notes:

- **A_Stop**<a id="A_Stop"></a>
  - Description:
  - Args:
  - Notes:

- **A_SpawnObject**<a id="A_SpawnObject"></a>
  - Description:
  - Args:
  - Notes:

- **A_MonsterProjectile**<a id="A_MonsterProjectile"></a>
  - Description:
  - Args:
  - Notes:

- **A_MonsterBulletAttack**<a id="A_MonsterBulletAttack"></a>
  - Description:
  - Args:
  - Notes:

- **A_MonsterMeleeAttack**<a id="A_MonsterMeleeAttack"></a>
  - Description:
  - Args:
  - Notes:

- **A_MonsterComboAttack**<a id="A_MonsterComboAttack"></a>
  - Description:
  - Args:
  - Notes:

- **A_SpawnMinion**<a id="A_SpawnMinion"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusDamage**<a id="A_RadiusDamage"></a>
  - Description:
  - Args:
  - Notes:

- **A_NoiseAlert**<a id="A_NoiseAlert"></a>
  - Description:
  - Args:
  - Notes:

- **A_HealChase**<a id="A_HealChase"></a>
  - Description:
  - Args:
  - Notes:

- **A_SeekTracer**<a id="A_SeekTracer"></a>
  - Description:
  - Args:
  - Notes:

- **A_FindTracer**<a id="A_FindTracer"></a>
  - Description:
  - Args:
  - Notes:

- **A_ClearTracer**<a id="A_ClearTracer"></a>
  - Description:
  - Args:
  - Notes:

- **A_MissileExplode**<a id="A_MissileExplode"></a>
  - Description:
  - Args:
  - Notes:

- **A_MissileCountdown**<a id="A_MissileCountdown"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfHealthBelow**<a id="A_JumpIfHealthBelow"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTargetInSight**<a id="A_JumpIfTargetInSight"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTargetCloser**<a id="A_JumpIfTargetCloser"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTargetAlive**<a id="A_JumpIfTargetAlive"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTracerInSight**<a id="A_JumpIfTracerInSight"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTracerCloser**<a id="A_JumpIfTracerCloser"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfTracerAlive**<a id="A_JumpIfTracerAlive"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfHasPower**<a id="A_JumpIfHasPower"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfHasArtifact**<a id="A_JumpIfHasArtifact"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfHasKey**<a id="A_JumpIfHasKey"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfWeaponOwned**<a id="A_JumpIfWeaponOwned"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfWeaponUsable**<a id="A_JumpIfWeaponUsable"></a>
  - Description:
  - Args:
  - Notes:

- **A_JumpIfWeaponPiece**<a id="A_JumpIfWeaponPiece"></a>
  - Description:
  - Args:
  - Notes:

- **A_DeathBallBounceEX**<a id="A_DeathBallBounceEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_TempestChain**<a id="A_TempestChain"></a>
  - Description:
  - Args:
  - Notes:

- **A_TempestSpray**<a id="A_TempestSpray"></a>
  - Description:
  - Args:
  - Notes:

- **A_KnightAttackEX**<a id="A_KnightAttackEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_ClinkRushEX**<a id="A_ClinkRushEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_ClinkAttackEX**<a id="A_ClinkAttackEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_MinotaurMeleeEX**<a id="A_MinotaurMeleeEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_TrollChargeStart**<a id="A_TrollChargeStart"></a>
  - Description:
  - Args:
  - Notes:

- **A_TrollCharge**<a id="A_TrollCharge"></a>
  - Description:
  - Args:
  - Notes:

- **A_SuperDemonAttack1**<a id="A_SuperDemonAttack1"></a>
  - Description:
  - Args:
  - Notes:

- **A_SuperDemonAttack2**<a id="A_SuperDemonAttack2"></a>
  - Description:
  - Args:
  - Notes:

- **A_CheckThrowBomb2**<a id="A_CheckThrowBomb2"></a>
  - Description:
  - Args:
  - Notes:

- **A_ShedShardEX**<a id="A_ShedShardEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardRaise**<a id="A_CentaurEXGuardRaise"></a>
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardHold**<a id="A_CentaurEXGuardHold"></a>
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardLower**<a id="A_CentaurEXGuardLower"></a>
  - Description:
  - Args:
  - Notes:

- **A_CentaurAttack2EX**<a id="A_CentaurAttack2EX"></a>
  - Description:
  - Args:
  - Notes:

- **A_SerpentHumpDecideEX**<a id="A_SerpentHumpDecideEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_BishopSpawnBlurEX**<a id="A_BishopSpawnBlurEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_FlyTarget**<a id="A_FlyTarget"></a>
  - Description:
  - Args:
  - Notes:

- **A_FlyChase**<a id="A_FlyChase"></a>
  - Description:
  - Args:
  - Notes:

## Weapon Action Definitions

- **A_WeaponSound**<a id="A_WeaponSound"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponProjectile**<a id="A_WeaponProjectile"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponBulletAttack**<a id="A_WeaponBulletAttack"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponMeleeAttack**<a id="A_WeaponMeleeAttack"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJump**<a id="A_WeaponJump"></a>
  - Description:
  - Args:
  - Notes:

- **A_ConsumeAmmo**<a id="A_ConsumeAmmo"></a>
  - Description:
  - Args:
  - Notes:

- **A_CheckAmmo**<a id="A_CheckAmmo"></a>
  - Description:
  - Args:
  - Notes:

- **A_RefireTo**<a id="A_RefireTo"></a>
  - Description:
  - Args:
  - Notes:

- **A_GunFlashTo**<a id="A_GunFlashTo"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponAlert**<a id="A_WeaponAlert"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfHasPower**<a id="A_WeaponJumpIfHasPower"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfHasArtifact**<a id="A_WeaponJumpIfHasArtifact"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfHasKey**<a id="A_WeaponJumpIfHasKey"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponOwned**<a id="A_WeaponJumpIfWeaponOwned"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponUsable**<a id="A_WeaponJumpIfWeaponUsable"></a>
  - Description:
  - Args:
  - Notes:

- **A_WeaponJumpIfWeaponPiece**<a id="A_WeaponJumpIfWeaponPiece"></a>
  - Description:
  - Args:
  - Notes:

- **A_GauntletAttackEX**<a id="A_GauntletAttackEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireGoldWandEX1**<a id="A_FireGoldWandEX1"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowEX1**<a id="A_FireCrossbowEX1"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowEX2**<a id="A_FireCrossbowEX2"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodEX1**<a id="A_FireSkullRodEX1"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodEX2**<a id="A_FireSkullRodEX2"></a>
  - Description:
  - Args:
  - Notes:

- **A_FirePhoenixEX2**<a id="A_FirePhoenixEX2"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireMaceEX1**<a id="A_FireMaceEX1"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireMaceEX2**<a id="A_FireMaceEX2"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireTempestWandPL1**<a id="A_FireTempestWandPL1"></a>
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckUp**<a id="A_FAxeCheckUp"></a>
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckUpG**<a id="A_FAxeCheckUpG"></a>
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckReady**<a id="A_FAxeCheckReady"></a>
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckReadyG**<a id="A_FAxeCheckReadyG"></a>
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckAtk**<a id="A_FAxeCheckAtk"></a>
  - Description:
  - Args:
  - Notes:

- **A_CMaceAttackEX**<a id="A_CMaceAttackEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_CShieldParry**<a id="A_CShieldParry"></a>
  - Description:
  - Args:
  - Notes:

- **A_CShieldGuard**<a id="A_CShieldGuard"></a>
  - Description:
  - Args:
  - Notes:

- **A_CShieldLower**<a id="A_CShieldLower"></a>
  - Description:
  - Args:
  - Notes:

- **A_CStaffDrainEX**<a id="A_CStaffDrainEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_FireConeEX**<a id="A_FireConeEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_MStaffAttackEX**<a id="A_MStaffAttackEX"></a>
  - Description:
  - Args:
  - Notes:

## Pickup Action Definitions

- **A_PickupHealth**<a id="A_PickupHealth"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupBasicArmor**<a id="A_PickupBasicArmor"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupHexenArmor**<a id="A_PickupHexenArmor"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupBackpack**<a id="A_PickupBackpack"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupWeapon**<a id="A_PickupWeapon"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupWeaponPiece**<a id="A_PickupWeaponPiece"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupAmmo**<a id="A_PickupAmmo"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupDualAmmo**<a id="A_PickupDualAmmo"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupPower**<a id="A_PickupPower"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupArtifact**<a id="A_PickupArtifact"></a>
  - Description:
  - Args:
  - Notes:

- **A_PickupKey**<a id="A_PickupKey"></a>
  - Description:
  - Args:
  - Notes:

## Power Action Definitions

- **A_PowerInvulnerableStart**<a id="A_PowerInvulnerableStart"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerInvulnerableTick**<a id="A_PowerInvulnerableTick"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerInvulnerableEnd**<a id="A_PowerInvulnerableEnd"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerInvisibilityStart**<a id="A_PowerInvisibilityStart"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerInvisibilityEnd**<a id="A_PowerInvisibilityEnd"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerStrengthStart**<a id="A_PowerStrengthStart"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerFlightStart**<a id="A_PowerFlightStart"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerFlightEnd**<a id="A_PowerFlightEnd"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerWeaponLevel2Start**<a id="A_PowerWeaponLevel2Start"></a>
  - Description:
  - Args:
  - Notes:

- **A_PowerWeaponLevel2End**<a id="A_PowerWeaponLevel2End"></a>
  - Description:
  - Args:
  - Notes:

## Artifact Action Definitions

- **A_ArtiGivePower**<a id="A_ArtiGivePower"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiHealth**<a id="A_ArtiHealth"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiEgg**<a id="A_ArtiEgg"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiSummon**<a id="A_ArtiSummon"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiTeleport**<a id="A_ArtiTeleport"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiTeleportOther**<a id="A_ArtiTeleportOther"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiBoostMana**<a id="A_ArtiBoostMana"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiBoostArmor**<a id="A_ArtiBoostArmor"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiBlastRadius**<a id="A_ArtiBlastRadius"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiFlechette**<a id="A_ArtiFlechette"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiThrowingBomb**<a id="A_ArtiThrowingBomb"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiThrowingBombEX**<a id="A_ArtiThrowingBombEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiPoisonBag**<a id="A_ArtiPoisonBag"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiFireBomb**<a id="A_ArtiFireBomb"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiHealingRadius**<a id="A_ArtiHealingRadius"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveArmor**<a id="A_RadiusGiveArmor"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveArmorEX**<a id="A_RadiusGiveArmorEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveHealth**<a id="A_RadiusGiveHealth"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveMana**<a id="A_RadiusGiveMana"></a>
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveManaEX**<a id="A_RadiusGiveManaEX"></a>
  - Description:
  - Args:
  - Notes:

- **A_ArtiPuzzleItem**<a id="A_ArtiPuzzleItem"></a>
  - Description:
  - Args:
  - Notes:
