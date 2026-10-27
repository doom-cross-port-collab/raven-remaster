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

### Codepointer

| Name                   | Origin            | Description |
|------------------------|-------------------|-------------|
| A_Look                 | Vanilla (Common)  | |
| A_Chase                | Vanilla (Common)  | |
| A_FaceTarget           | Vanilla (Common)  | |
| A_Scream               | Vanilla (Common)  | |
| A_Pain                 | Vanilla (Common)  | |
| A_FreeTargMobj         | Vanilla (Common)  | |
| A_RestoreSpecialThing1 | Vanilla (Common)  | |
| A_RestoreSpecialThing2 | Vanilla (Common)  | |
| A_RestoreArtifact      | Vanilla (Common)  | |
| A_HideThing            | Vanilla (Common)  | |
| A_UnHideThing          | Vanilla (Common)  | |
| A_NoBlocking           | Vanilla (Common)  | |
| A_AddPlayerCorpse      | Vanilla (Common)  | |
| A_SkullPop             | Vanilla (Common)  | |
| A_CheckBurnGone        | Vanilla (Common)  | |
| A_CheckSkullFloor      | Vanilla (Common)  | |
| A_CheckSkullDone       | Vanilla (Common)  | |
| A_ESound               | Modified          | Now plays the actor's 'activesound', instead of doing a hardcoded branch based on mobj type. |
| A_PlayerPain           | Vanilla (Heretic) | |
| A_PlayerScream         | Vanilla (Heretic) | |
| A_BossDeath            | Vanilla (Heretic) | |
| A_Explode              | Modified          | Branches based on actor's behavior field, instead of its mobjtype |
| A_PodPain              | Vanilla (Heretic) | |
| A_RemovePod            | Vanilla (Heretic) | |
| A_MakePod              | Vanilla (Heretic) | |
| A_InitKeyGizmo         | Vanilla (Heretic) | |
| A_VolcanoSet           | Vanilla (Heretic) | |
| A_VolcanoBlast         | Vanilla (Heretic) | |
| A_BeastPuff            | Vanilla (Heretic) | |
| A_VolcBallImpact       | Vanilla (Heretic) | |
| A_SpawnTeleGlitter     | Vanilla (Heretic) | |
| A_SpawnTeleGlitter2    | Vanilla (Heretic) | |
| A_AccTeleGlitter       | Vanilla (Heretic) | |
| A_SpawnRippers         | Vanilla (Heretic) | |
| A_MacePL1Check         | Vanilla (Heretic) | |
| A_MaceBallImpact       | Vanilla (Heretic) | |
| A_MaceBallImpact2      | Vanilla (Heretic) | |
| A_DeathBallImpact      | Vanilla (Heretic) | |
| A_SkullRodPL2Seek      | Vanilla (Heretic) | |
| A_AddPlayerRain        | Vanilla (Heretic) | |
| A_HideInCeiling        | Vanilla (Heretic) | |
| A_SkullRodStorm        | Vanilla (Heretic) | |
| A_RainImpact           | Vanilla (Heretic) | |
| A_PhoenixPuff          | Vanilla (Heretic) | |
| A_FlameEnd             | Vanilla (Heretic) | |
| A_FloatPuff            | Vanilla (Heretic) | |
| A_BoltSpark            | Vanilla (Heretic) | |
| A_FlameSnd             | Vanilla (Heretic) | |
| A_Feathers             | Vanilla (Heretic) | |
| A_ChicLook             | Vanilla (Heretic) | |
| A_ChicChase            | Vanilla (Heretic) | |
| A_ChicPain             | Vanilla (Heretic) | |
| A_ChicAttack           | Vanilla (Heretic) | |
| A_MummyAttack          | Vanilla (Heretic) | |
| A_MummyAttack2         | Vanilla (Heretic) | |
| A_MummySoul            | Vanilla (Heretic) | |
| A_ContMobjSound        | Vanilla (Heretic) | |
| A_MummyFX1Seek         | Vanilla (Heretic) | |
| A_BeastAttack          | Vanilla (Heretic) | |
| A_SnakeAttack          | Vanilla (Heretic) | |
| A_SnakeAttack2         | Vanilla (Heretic) | |
| A_LichAttack           | Vanilla (Heretic) | Named A_HeadAttack in vanilla; renamed to avoid ambiguity with Cacodemon attack function (matches GZDoom's naming) |
| A_HeadIceImpact        | Vanilla (Heretic) | |
| A_HeadFireGrow         | Vanilla (Heretic) | |
| A_WhirlwindSeek        | Vanilla (Heretic) | |
| A_ClinkAttack          | Vanilla (Heretic) | |
| A_WizAtk1              | Vanilla (Heretic) | |
| A_WizAtk2              | Vanilla (Heretic) | |
| A_WizAtk3              | Vanilla (Heretic) | |
| A_GhostOff             | Vanilla (Heretic) | |
| A_ImpMeAttack          | Vanilla (Heretic) | |
| A_ImpMsAttack          | Vanilla (Heretic) | |
| A_ImpMsAttack2         | Vanilla (Heretic) | |
| A_ImpDeath             | Vanilla (Heretic) | |
| A_ImpXDeath1           | Vanilla (Heretic) | |
| A_ImpXDeath2           | Vanilla (Heretic) | |
| A_ImpExplode           | Vanilla (Heretic) | |
| A_KnightAttack         | Vanilla (Heretic) | |
| A_DripBlood            | Vanilla (Heretic) | |
| A_Sor1Chase            | Vanilla (Heretic) | |
| A_Sor1Pain             | Vanilla (Heretic) | |
| A_Srcr1Attack          | Vanilla (Heretic) | |
| A_SorZap               | Vanilla (Heretic) | |
| A_SorcererRise         | Vanilla (Heretic) | |
| A_SorRise              | Vanilla (Heretic) | |
| A_SorSightSnd          | Vanilla (Heretic) | |
| A_Srcr2Decide          | Vanilla (Heretic) | |
| A_Srcr2Attack          | Vanilla (Heretic) | |
| A_Sor2DthInit          | Vanilla (Heretic) | |
| A_SorDSph              | Vanilla (Heretic) | |
| A_Sor2DthLoop          | Vanilla (Heretic) | |
| A_SorDExp              | Vanilla (Heretic) | |
| A_SorDBon              | Vanilla (Heretic) | |
| A_BlueSpark            | Vanilla (Heretic) | |
| A_GenWizard            | Vanilla (Heretic) | |
| A_MinotaurAtk1         | Vanilla (Heretic) | |
| A_MinotaurDecide       | Vanilla (Heretic) | |
| A_MinotaurAtk2         | Vanilla (Heretic) | |
| A_MinotaurAtk3         | Vanilla (Heretic) | |
| A_MinotaurCharge       | Vanilla (Heretic) | |
| A_MntrFloorFire        | Vanilla (Heretic) | |
| A_FlameCheck           | Vanilla (Hexen)   | |
| A_Summon               | Vanilla (Hexen)   | |
| A_ThrustInitUp         | Vanilla (Hexen)   | |
| A_ThrustInitDn         | Vanilla (Hexen)   | |
| A_ThrustRaise          | Vanilla (Hexen)   | |
| A_ThrustBlock          | Vanilla (Hexen)   | |
| A_ThrustImpale         | Vanilla (Hexen)   | |
| A_ThrustLower          | Vanilla (Hexen)   | |
| A_TeloSpawnC           | Vanilla (Hexen)   | |
| A_TeloSpawnB           | Vanilla (Hexen)   | |
| A_TeloSpawnA           | Vanilla (Hexen)   | |
| A_TeloSpawnD           | Vanilla (Hexen)   | |
| A_CheckTeleRing        | Vanilla (Hexen)   | |
| A_FogSpawn             | Vanilla (Hexen)   | |
| A_FogMove              | Vanilla (Hexen)   | |
| A_Quake                | Vanilla (Hexen)   | |
| A_PoisonBagInit        | Vanilla (Hexen)   | |
| A_PoisonBagDamage      | Vanilla (Hexen)   | |
| A_PoisonBagCheck       | Vanilla (Hexen)   | |
| A_CheckThrowBomb       | Vanilla (Hexen)   | |
| A_NoGravity            | Vanilla (Hexen)   | |
| A_PotteryExplode       | Vanilla (Hexen)   | |
| A_PotteryChooseBit     | Vanilla (Hexen)   | |
| A_PotteryCheck         | Vanilla (Hexen)   | |
| A_CorpseBloodDrip      | Vanilla (Hexen)   | |
| A_CorpseExplode        | Vanilla (Hexen)   | |
| A_LeafSpawn            | Vanilla (Hexen)   | |
| A_LeafThrust           | Vanilla (Hexen)   | |
| A_LeafCheck            | Vanilla (Hexen)   | |
| A_BridgeInit           | Vanilla (Hexen)   | |
| A_BridgeOrbit          | Vanilla (Hexen)   | |
| A_TreeDeath            | Vanilla (Hexen)   | |
| A_PoisonShroom         | Vanilla (Hexen)   | |
| A_SoAExplode           | Vanilla (Hexen)   | |
| A_BellReset1           | Vanilla (Hexen)   | |
| A_BellReset2           | Vanilla (Hexen)   | |
| A_FSwordFlames         | Vanilla (Hexen)   | |
| A_CStaffMissileSlither | Vanilla (Hexen)   | |
| A_CFlameRotate         | Vanilla (Hexen)   | |
| A_CFlamePuff           | Vanilla (Hexen)   | |
| A_CFlameMissile        | Vanilla (Hexen)   | |
| A_CHolySeek            | Vanilla (Hexen)   | |
| A_CHolyCheckScream     | Vanilla (Hexen)   | |
| A_CHolyTail            | Vanilla (Hexen)   | |
| A_CHolySpawnPuff       | Vanilla (Hexen)   | |
| A_CHolyAttack2         | Vanilla (Hexen)   | |
| A_LightningZap         | Vanilla (Hexen)   | |
| A_LightningClip        | Vanilla (Hexen)   | |
| A_LightningRemove      | Vanilla (Hexen)   | |
| A_LastZap              | Vanilla (Hexen)   | |
| A_ZapMimic             | Vanilla (Hexen)   | |
| A_MStaffWeave          | Vanilla (Hexen)   | |
| A_MStaffTrack          | Vanilla (Hexen)   | |
| A_SnoutAttack          | Vanilla (Hexen)   | |
| A_ShedShard            | Vanilla (Hexen)   | |
| A_FreezeDeath          | Vanilla (Hexen)   | |
| A_FreezeDeathChunks    | Vanilla (Hexen)   | |
| A_SpeedFade            | Vanilla (Hexen)   | |
| A_IceSetTics           | Vanilla (Hexen)   | |
| A_IceCheckHeadDone     | Vanilla (Hexen)   | |
| A_PigPain              | Vanilla (Hexen)   | |
| A_PigLook              | Vanilla (Hexen)   | |
| A_PigChase             | Vanilla (Hexen)   | |
| A_PigAttack            | Vanilla (Hexen)   | |
| A_QueueCorpse          | Vanilla (Hexen)   | |
| A_CentaurAttack        | Vanilla (Hexen)   | |
| A_CentaurAttack2       | Vanilla (Hexen)   | |
| A_SetReflective        | Vanilla (Hexen)   | |
| A_CentaurDefend        | Vanilla (Hexen)   | |
| A_UnSetReflective      | Vanilla (Hexen)   | |
| A_CentaurDropStuff     | Vanilla (Hexen)   | |
| A_CheckFloor           | Vanilla (Hexen)   | |
| A_DemonAttack1         | Vanilla (Hexen)   | |
| A_DemonAttack2         | Vanilla (Hexen)   | |
| A_DemonDeath           | Vanilla (Hexen)   | |
| A_Demon2Death          | Vanilla (Hexen)   | |
| A_WraithRaiseInit      | Vanilla (Hexen)   | |
| A_WraithRaise          | Vanilla (Hexen)   | |
| A_WraithInit           | Vanilla (Hexen)   | |
| A_WraithLook           | Vanilla (Hexen)   | |
| A_WraithChase          | Vanilla (Hexen)   | |
| A_WraithFX3            | Vanilla (Hexen)   | |
| A_WraithMelee          | Vanilla (Hexen)   | |
| A_WraithMissile        | Vanilla (Hexen)   | |
| A_WraithFX2            | Vanilla (Hexen)   | |
| A_MinotaurFade1        | Vanilla (Hexen)   | |
| A_MinotaurFade2        | Vanilla (Hexen)   | |
| A_MinotaurLook         | Vanilla (Hexen)   | |
| A_MinotaurChase        | Vanilla (Hexen)   | |
| A_MinotaurRoam         | Vanilla (Hexen)   | |
| A_MinotaurAtk1         | Vanilla (Hexen)   | |
| A_MinotaurDecide       | Vanilla (Hexen)   | |
| A_MinotaurAtk2         | Vanilla (Hexen)   | |
| A_MinotaurAtk3         | Vanilla (Hexen)   | |
| A_MinotaurCharge       | Vanilla (Hexen)   | |
| A_SmokePuffExit        | Vanilla (Hexen)   | |
| A_MinotaurFade0        | Vanilla (Hexen)   | |
| A_MntrFloorFire        | Vanilla (Hexen)   | |
| A_SerpentChase         | Vanilla (Hexen)   | |
| A_SerpentHumpDecide    | Vanilla (Hexen)   | |
| A_SerpentUnHide        | Vanilla (Hexen)   | |
| A_SerpentRaiseHump     | Vanilla (Hexen)   | |
| A_SerpentLowerHump     | Vanilla (Hexen)   | |
| A_SerpentHide          | Vanilla (Hexen)   | |
| A_SerpentBirthScream   | Vanilla (Hexen)   | |
| A_SetShootable         | Vanilla (Hexen)   | |
| A_SerpentCheckForAttack| Vanilla (Hexen)   | |
| A_UnSetShootable       | Vanilla (Hexen)   | |
| A_SerpentDiveSound     | Vanilla (Hexen)   | |
| A_SerpentWalk          | Vanilla (Hexen)   | |
| A_SerpentChooseAttack  | Vanilla (Hexen)   | |
| A_SerpentMeleeAttack   | Vanilla (Hexen)   | |
| A_SerpentMissileAttack | Vanilla (Hexen)   | |
| A_SerpentHeadPop       | Vanilla (Hexen)   | |
| A_SerpentSpawnGibs     | Vanilla (Hexen)   | |
| A_SerpentHeadCheck     | Vanilla (Hexen)   | |
| A_FloatGib             | Vanilla (Hexen)   | |
| A_DelayGib             | Vanilla (Hexen)   | |
| A_SinkGib              | Vanilla (Hexen)   | |
| A_BishopDecide         | Vanilla (Hexen)   | |
| A_BishopDoBlur         | Vanilla (Hexen)   | |
| A_BishopSpawnBlur      | Vanilla (Hexen)   | |
| A_BishopChase          | Vanilla (Hexen)   | |
| A_BishopAttack         | Vanilla (Hexen)   | |
| A_BishopAttack2        | Vanilla (Hexen)   | |
| A_BishopPainBlur       | Vanilla (Hexen)   | |
| A_BishopPuff           | Vanilla (Hexen)   | |
| A_SetAltShadow         | Vanilla (Hexen)   | |
| A_BishopMissileWeave   | Vanilla (Hexen)   | |
| A_BishopMissileSeek    | Vanilla (Hexen)   | |
| A_DragonInitFlight     | Vanilla (Hexen)   | |
| A_DragonFlap           | Vanilla (Hexen)   | |
| A_DragonFlight         | Vanilla (Hexen)   | |
| A_DragonAttack         | Vanilla (Hexen)   | |
| A_DragonPain           | Vanilla (Hexen)   | |
| A_DragonCheckCrash     | Vanilla (Hexen)   | |
| A_DragonFX2            | Vanilla (Hexen)   | |
| A_EttinAttack          | Vanilla (Hexen)   | |
| A_DropMace             | Vanilla (Hexen)   | |
| A_FiredRocks           | Vanilla (Hexen)   | |
| A_UnSetInvulnerable    | Vanilla (Hexen)   | |
| A_FiredChase           | Vanilla (Hexen)   | |
| A_FiredAttack          | Vanilla (Hexen)   | |
| A_FiredSplotch         | Vanilla (Hexen)   | |
| A_SmBounce             | Vanilla (Hexen)   | |
| A_IceGuyLook           | Vanilla (Hexen)   | |
| A_IceGuyChase          | Vanilla (Hexen)   | |
| A_IceGuyAttack         | Vanilla (Hexen)   | |
| A_IceGuyDie            | Vanilla (Hexen)   | |
| A_IceGuyMissilePuff    | Vanilla (Hexen)   | |
| A_IceGuyMissileExplode | Vanilla (Hexen)   | |
| A_ClassBossHealth      | Vanilla (Hexen)   | |
| A_FastChase            | Vanilla (Hexen)   | |
| A_FighterAttack        | Vanilla (Hexen)   | |
| A_ClericAttack         | Vanilla (Hexen)   | |
| A_MageAttack           | Vanilla (Hexen)   | |
| A_SorcSpinBalls        | Vanilla (Hexen)   | |
| A_SpeedBalls           | Vanilla (Hexen)   | |
| A_SpawnFizzle          | Vanilla (Hexen)   | |
| A_SorcBossAttack       | Vanilla (Hexen)   | |
| A_SorcBallOrbit        | Vanilla (Hexen)   | |
| A_SorcBallPop          | Vanilla (Hexen)   | |
| A_BounceCheck          | Vanilla (Hexen)   | |
| A_SorcFX1Seek          | Vanilla (Hexen)   | |
| A_SorcFX2Split         | Vanilla (Hexen)   | |
| A_SorcFX2Orbit         | Vanilla (Hexen)   | |
| A_SorcererBishopEntry  | Vanilla (Hexen)   | |
| A_SpawnBishop          | Vanilla (Hexen)   | |
| A_SorcFX4Check         | Vanilla (Hexen)   | |
| A_KoraxStep2           | Vanilla (Hexen)   | |
| A_KoraxChase           | Vanilla (Hexen)   | |
| A_KoraxStep            | Vanilla (Hexen)   | |
| A_KoraxDecide          | Vanilla (Hexen)   | |
| A_KoraxMissile         | Vanilla (Hexen)   | |
| A_KoraxCommand         | Vanilla (Hexen)   | |
| A_KoraxBonePop         | Vanilla (Hexen)   | |
| A_KSpiritRoam          | Vanilla (Hexen)   | |
| A_KBoltRaise           | Vanilla (Hexen)   | |
| A_KBolt                | Vanilla (Hexen)   | |
| A_BatSpawnInit         | Vanilla (Hexen)   | |
| A_BatSpawn             | Vanilla (Hexen)   | |
| A_BatMove              | Vanilla (Hexen)   | |
| A_FlyTarget            | New               | Likely identical to GZDoom's A_FlySearch; name taken from original source code |
| A_FlyChase             | New               | Likely identical to GZDoom's A_FlyBuzz; name taken from original source code |


## Codepointers EX

| Name                     | Origin | Description |
|--------------------------|--------|-------------|
| A_PlaySound              |        | |
| A_FaceTargetSound        |        | |
| A_RandomJump             |        | |
| A_SpawnObject            |        | |
| A_MonsterProjectile      |        | |
| A_MonsterBulletAttack    |        | |
| A_MonsterMeleeAttack     |        | |
| A_MonsterComboAttack     |        | |
| A_SpawnMinion            |        | |
| A_RadiusDamage           |        | |
| A_SeekTracer             |        | |
| A_FindTracer             |        | |
| A_ClearTracer            |        | |
| A_MissileExplode         |        | |
| A_MissileCountdown       |        | |
| A_DeathBallBounceEX      |        | |
| A_TempestChain           |        | |
| A_TempestSpray           |        | |
| A_KnightAttackEX         |        | |
| A_ClinkRushEX            |        | |
| A_ClinkAttackEX          |        | |
| A_MinotaurMeleeEX        |        | |
| A_TrollChargeStart       |        | |
| A_TrollCharge            |        | |
| A_SuperDemonAttack1      |        | |
| A_SuperDemonAttack2      |        | |
| A_ShedShardEX            |        | |
| A_CentaurEXGuardRaise    |        | |
| A_CentaurEXGuardHold     |        | |
| A_CentaurEXGuardLower    |        | |
| A_CentaurAttack2EX       |        | |

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

### Codepointers EX

| Name                    | Origin | Description |
|-------------------------|--------|-------------|
| A_InvulnStandardStart   |        | |
| A_InvulnStandardEnd     |        | |
| A_InvulnPhasedTick      |        | |
| A_InvulnPhasedDraw      |        | PlayerVis |
| A_InvulnPhasedEnd       |        | |
| A_InvulnReflectiveStart |        | |
| A_InvulnReflectiveEnd   |        | |

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

### Codepointers

| Name                 | Origin            | Description |
|----------------------|-------------------|-|
| A_Light0             | Vanilla (Common)  | |
| A_Light1             | Vanilla (Common)  | |
| A_Light2             | Vanilla (Common)  | |
| A_WeaponReady        | Vanilla (Common)  | |
| A_Lower              | Vanilla (Common)  | |
| A_Raise              | Vanilla (Common)  | |
| A_ReFire             | Vanilla (Common)  | |
| A_StaffAttackPL1     | Vanilla (Heretic) | |
| A_StaffAttackPL2     | Vanilla (Heretic) | |
| A_BeakReady          | Vanilla (Heretic) | |
| A_BeakRaise          | Vanilla (Heretic) | |
| A_BeakAttackPL1      | Vanilla (Heretic) | |
| A_BeakAttackPL2      | Vanilla (Heretic) | |
| A_GauntletAttack     | Vanilla (Heretic) | |
| A_FireBlasterPL1     | Vanilla (Heretic) | |
| A_FireBlasterPL2     | Vanilla (Heretic) | |
| A_FireMacePL1        | Vanilla (Heretic) | |
| A_FireMacePL2        | Vanilla (Heretic) | |
| A_FireSkullRodPL1    | Vanilla (Heretic) | |
| A_FireSkullRodPL2    | Vanilla (Heretic) | |
| A_FireGoldWandPL1    | Vanilla (Heretic) | |
| A_FireGoldWandPL2    | Vanilla (Heretic) | |
| A_FirePhoenixPL1     | Vanilla (Heretic) | |
| A_InitPhoenixPL2     | Vanilla (Heretic) | |
| A_FirePhoenixPL2     | Vanilla (Heretic) | |
| A_ShutdownPhoenixPL2 | Vanilla (Heretic) | |
| A_FireCrossbowPL1    | Vanilla (Heretic) | |
| A_FireCrossbowPL2    | Vanilla (Heretic) | |
| A_FPunchAttack       | Vanilla (Hexen)   | |
| A_FAxeCheckUp        | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckUpG       | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckReady     | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckReadyG    | New               | Identical to GZDoom's function of the same name. |
| A_FAxeCheckAtk       | New               | Identical to GZDoom's function of the same name. |
| A_FAxeAttack         | Vanilla (Hexen)   | |
| A_SnoutAttack        | Vanilla (Hexen)   | |
| A_FHammerAttack      | Vanilla (Hexen)   | |
| A_FHammerThrow       | Vanilla (Hexen)   | |
| A_FSwordAttack       | Vanilla (Hexen)   | |
| A_CMaceAttack        | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_CStaffInitBlink    | Vanilla (Hexen)   | |
| A_CStaffCheckBlink   | Vanilla (Hexen)   | |
| A_CStaffCheck        | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_CStaffAttack       | Vanilla (Hexen)   | |
| A_CFlameAttack       | Vanilla (Hexen)   | |
| A_CHolyAttack        | Vanilla (Hexen)   | |
| A_CHolyPalette       | Vanilla (Hexen)   | |
| A_MWandAttack        | Vanilla (Hexen)   | |
| A_LightningReady     | Vanilla (Hexen)   | |
| A_MLightningAttack   | Vanilla (Hexen)   | |
| A_MStaffAttack       | Modified          | Now has args for extended behavior; see "Actions (New)" tab for details |
| A_MStaffPalette      | Vanilla (Hexen)   | |
| A_FireConePL1        | Vanilla (Hexen)   | |

### Codepointers EX

| Name                 | Origin | Description |
|----------------------|--------|-|
| A_WeaponSound        |        | |
| A_WeaponProjectile   |        | |
| A_WeaponBulletAttack |        | |
| A_WeaponMeleeAttack  |        | |
| A_ConsumeAmmo        |        | |
| A_GauntletAttackEX   |        | |
| A_FireGoldWandEX1    |        | |
| A_FireCrossbowEX1    |        | |
| A_FireCrossbowEX2    |        | |
| A_FireSkullRodEX1    |        | |
| A_FireSkullRodEX2    |        | |
| A_FirePhoenixEX2     |        | |
| A_FireMaceEX1        |        | |
| A_FireMaceEX2        |        | |
| A_FireTempestWandPL1 |        | |
| A_CShieldParry       |        | |
| A_CShieldGuard       |        | |
| A_CShieldLower       |        | |
| A_FireConeEX1        |        | |
| A_MStaffAttackEX     |        | |

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

### Codepointers EX

| Name                | Origin | Description |
|---------------------|--------|-------------|
| A_PickupHealth      |        | |
| A_PickupBasicArmor  |        | |
| A_PickupHexenArmor  |        | |
| A_PickupBackpack    |        | |
| A_PickupWeapon      |        | |
| A_PickupWeaponPiece |        | |
| A_PickupAmmo        |        | |
| A_PickupDualAmmo    |        | |
| A_PickupPower       |        | |
| A_PickupArtifact    |        | |
| A_PickupKey         |        | |

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

### Codepointers EX

| Name                     | Origin | Description |
|--------------------------|--------|-------------|
| A_PowerInvulnerableStart |        | |
| A_PowerInvulnerableTick  |        | |
| A_PowerInvulnerableEnd   |        | |
| A_PowerInvisibilityStart |        | |
| A_PowerInvisibilityEnd   |        | |
| A_PowerStrengthStart     |        | |
| A_PowerFlightStart       |        | |
| A_PowerFlightEnd         |        | |
| A_PowerWeaponLevel2Start |        | |
| A_PowerWeaponLevel2End   |        | |

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

### Codepointers EX

| Name                | Origin | Description |
|---------------------|--------|-------------|
| A_ArtiGivePower     |        | |
| A_ArtiHealth        |        | |
| A_ArtiEgg           |        | |
| A_ArtiSummon        |        | |
| A_ArtiTeleport      |        | |
| A_ArtiTeleportOther |        | |
| A_ArtiBoostMana     |        | |
| A_ArtiBoostArmor    |        | |
| A_ArtiBlastRadius   |        | |
| A_ArtiFlechette     |        | |
| A_ArtiThrowingBomb  |        | |
| A_ArtiThrowingBombEX|        | |
| A_ArtiPoisonBag     |        | |
| A_ArtiFireBomb      |        | |
| A_ArtiHealingRadius |        | |
| A_RadiusGiveArmor   |        | |
| A_RadiusGiveArmorEX |        | |
| A_RadiusGiveHealth  |        | |
| A_RadiusGiveMana    |        | |
| A_RadiusGiveManaEX  |        | |
| A_ArtiPuzzleItem    |        | |

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

## Actor Codepointer Overview

- **A_Look**
  - Description:
  - Args:
  - Notes:

- **A_Chase**
  - Description:
  - Args:
  - Notes:

- **A_FaceTarget**
  - Description:
  - Args:
  - Notes:

- **A_Scream**
  - Description:
  - Args:
  - Notes:

- **A_Pain**
  - Description:
  - Args:
  - Notes:

- **A_FreeTargMobj**
  - Description:
  - Args:
  - Notes:

- **A_RestoreSpecialThing1**
  - Description:
  - Args:
  - Notes:

- **A_RestoreSpecialThing2**
  - Description:
  - Args:
  - Notes:

- **A_RestoreArtifact**
  - Description:
  - Args:
  - Notes:

- **A_HideThing**
  - Description:
  - Args:
  - Notes:

- **A_UnHideThing**
  - Description:
  - Args:
  - Notes:

- **A_NoBlocking**
  - Description:
  - Args:
  - Notes:

- **A_AddPlayerCorpse**
  - Description:
  - Args:
  - Notes:

- **A_SkullPop**
  - Description:
  - Args:
  - Notes:

- **A_CheckBurnGone**
  - Description:
  - Args:
  - Notes:

- **A_CheckSkullFloor**
  - Description:
  - Args:
  - Notes:

- **A_CheckSkullDone**
  - Description:
  - Args:
  - Notes:

- **A_ESound**
  - Description:
  - Args:
  - Notes:

- **A_PlayerPain**
  - Description:
  - Args:
  - Notes:

- **A_PlayerScream**
  - Description:
  - Args:
  - Notes:

- **A_BossDeath**
  - Description:
  - Args:
  - Notes:

- **A_Explode**
  - Description:
  - Args:
  - Notes:

- **A_PodPain**
  - Description:
  - Args:
  - Notes:

- **A_RemovePod**
  - Description:
  - Args:
  - Notes:

- **A_MakePod**
  - Description:
  - Args:
  - Notes:

- **A_InitKeyGizmo**
  - Description:
  - Args:
  - Notes:

- **A_VolcanoSet**
  - Description:
  - Args:
  - Notes:

- **A_VolcanoBlast**
  - Description:
  - Args:
  - Notes:

- **A_BeastPuff**
  - Description:
  - Args:
  - Notes:

- **A_VolcBallImpact**
  - Description:
  - Args:
  - Notes:

- **A_SpawnTeleGlitter**
  - Description:
  - Args:
  - Notes:

- **A_SpawnTeleGlitter2**
  - Description:
  - Args:
  - Notes:

- **A_AccTeleGlitter**
  - Description:
  - Args:
  - Notes:

- **A_SpawnRippers**
  - Description:
  - Args:
  - Notes:

- **A_MacePL1Check**
  - Description:
  - Args:
  - Notes:

- **A_MaceBallImpact**
  - Description:
  - Args:
  - Notes:

- **A_MaceBallImpact2**
  - Description:
  - Args:
  - Notes:

- **A_DeathBallImpact**
  - Description:
  - Args:
  - Notes:

- **A_SkullRodPL2Seek**
  - Description:
  - Args:
  - Notes:

- **A_AddPlayerRain**
  - Description:
  - Args:
  - Notes:

- **A_HideInCeiling**
  - Description:
  - Args:
  - Notes:

- **A_SkullRodStorm**
  - Description:
  - Args:
  - Notes:

- **A_RainImpact**
  - Description:
  - Args:
  - Notes:

- **A_PhoenixPuff**
  - Description:
  - Args:
  - Notes:

- **A_FlameEnd**
  - Description:
  - Args:
  - Notes:

- **A_FloatPuff**
  - Description:
  - Args:
  - Notes:

- **A_BoltSpark**
  - Description:
  - Args:
  - Notes:

- **A_FlameSnd**
  - Description:
  - Args:
  - Notes:

- **A_Feathers**
  - Description:
  - Args:
  - Notes:

- **A_ChicLook**
  - Description:
  - Args:
  - Notes:

- **A_ChicChase**
  - Description:
  - Args:
  - Notes:

- **A_ChicPain**
  - Description:
  - Args:
  - Notes:

- **A_ChicAttack**
  - Description:
  - Args:
  - Notes:

- **A_MummyAttack**
  - Description:
  - Args:
  - Notes:

- **A_MummyAttack2**
  - Description:
  - Args:
  - Notes:

- **A_MummySoul**
  - Description:
  - Args:
  - Notes:

- **A_ContMobjSound**
  - Description:
  - Args:
  - Notes:

- **A_MummyFX1Seek**
  - Description:
  - Args:
  - Notes:

- **A_BeastAttack**
  - Description:
  - Args:
  - Notes:

- **A_SnakeAttack**
  - Description:
  - Args:
  - Notes:

- **A_SnakeAttack2**
  - Description:
  - Args:
  - Notes:

- **A_LichAttack**
  - Description:
  - Args:
  - Notes:

- **A_HeadIceImpact**
  - Description:
  - Args:
  - Notes:

- **A_HeadFireGrow**
  - Description:
  - Args:
  - Notes:

- **A_WhirlwindSeek**
  - Description:
  - Args:
  - Notes:

- **A_ClinkAttack**
  - Description:
  - Args:
  - Notes:

- **A_WizAtk1**
  - Description:
  - Args:
  - Notes:

- **A_WizAtk2**
  - Description:
  - Args:
  - Notes:

- **A_WizAtk3**
  - Description:
  - Args:
  - Notes:

- **A_GhostOff**
  - Description:
  - Args:
  - Notes:

- **A_ImpMeAttack**
  - Description:
  - Args:
  - Notes:

- **A_ImpMsAttack**
  - Description:
  - Args:
  - Notes:

- **A_ImpMsAttack2**
  - Description:
  - Args:
  - Notes:

- **A_ImpDeath**
  - Description:
  - Args:
  - Notes:

- **A_ImpXDeath1**
  - Description:
  - Args:
  - Notes:

- **A_ImpXDeath2**
  - Description:
  - Args:
  - Notes:

- **A_ImpExplode**
  - Description:
  - Args:
  - Notes:

- **A_KnightAttack**
  - Description:
  - Args:
  - Notes:

- **A_DripBlood**
  - Description:
  - Args:
  - Notes:

- **A_Sor1Chase**
  - Description:
  - Args:
  - Notes:

- **A_Sor1Pain**
  - Description:
  - Args:
  - Notes:

- **A_Srcr1Attack**
  - Description:
  - Args:
  - Notes:

- **A_SorZap**
  - Description:
  - Args:
  - Notes:

- **A_SorcererRise**
  - Description:
  - Args:
  - Notes:

- **A_SorRise**
  - Description:
  - Args:
  - Notes:

- **A_SorSightSnd**
  - Description:
  - Args:
  - Notes:

- **A_Srcr2Decide**
  - Description:
  - Args:
  - Notes:

- **A_Srcr2Attack**
  - Description:
  - Args:
  - Notes:

- **A_Sor2DthInit**
  - Description:
  - Args:
  - Notes:

- **A_SorDSph**
  - Description:
  - Args:
  - Notes:

- **A_Sor2DthLoop**
  - Description:
  - Args:
  - Notes:

- **A_SorDExp**
  - Description:
  - Args:
  - Notes:

- **A_SorDBon**
  - Description:
  - Args:
  - Notes:

- **A_BlueSpark**
  - Description:
  - Args:
  - Notes:

- **A_GenWizard**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk1**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurDecide**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk2**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk3**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurCharge**
  - Description:
  - Args:
  - Notes:

- **A_MntrFloorFire**
  - Description:
  - Args:
  - Notes:

- **A_FlameCheck**
  - Description:
  - Args:
  - Notes:

- **A_Summon**
  - Description:
  - Args:
  - Notes:

- **A_ThrustInitUp**
  - Description:
  - Args:
  - Notes:

- **A_ThrustInitDn**
  - Description:
  - Args:
  - Notes:

- **A_ThrustRaise**
  - Description:
  - Args:
  - Notes:

- **A_ThrustBlock**
  - Description:
  - Args:
  - Notes:

- **A_ThrustImpale**
  - Description:
  - Args:
  - Notes:

- **A_ThrustLower**
  - Description:
  - Args:
  - Notes:

- **A_TeloSpawnC**
  - Description:
  - Args:
  - Notes:

- **A_TeloSpawnB**
  - Description:
  - Args:
  - Notes:

- **A_TeloSpawnA**
  - Description:
  - Args:
  - Notes:

- **A_TeloSpawnD**
  - Description:
  - Args:
  - Notes:

- **A_CheckTeleRing**
  - Description:
  - Args:
  - Notes:

- **A_FogSpawn**
  - Description:
  - Args:
  - Notes:

- **A_FogMove**
  - Description:
  - Args:
  - Notes:

- **A_Quake**
  - Description:
  - Args:
  - Notes:

- **A_PoisonBagInit**
  - Description:
  - Args:
  - Notes:

- **A_PoisonBagDamage**
  - Description:
  - Args:
  - Notes:

- **A_PoisonBagCheck**
  - Description:
  - Args:
  - Notes:

- **A_CheckThrowBomb**
  - Description:
  - Args:
  - Notes:

- **A_NoGravity**
  - Description:
  - Args:
  - Notes:

- **A_PotteryExplode**
  - Description:
  - Args:
  - Notes:

- **A_PotteryChooseBit**
  - Description:
  - Args:
  - Notes:

- **A_PotteryCheck**
  - Description:
  - Args:
  - Notes:

- **A_CorpseBloodDrip**
  - Description:
  - Args:
  - Notes:

- **A_CorpseExplode**
  - Description:
  - Args:
  - Notes:

- **A_LeafSpawn**
  - Description:
  - Args:
  - Notes:

- **A_LeafThrust**
  - Description:
  - Args:
  - Notes:

- **A_LeafCheck**
  - Description:
  - Args:
  - Notes:

- **A_BridgeInit**
  - Description:
  - Args:
  - Notes:

- **A_BridgeOrbit**
  - Description:
  - Args:
  - Notes:

- **A_TreeDeath**
  - Description:
  - Args:
  - Notes:

- **A_PoisonShroom**
  - Description:
  - Args:
  - Notes:

- **A_SoAExplode**
  - Description:
  - Args:
  - Notes:

- **A_BellReset1**
  - Description:
  - Args:
  - Notes:

- **A_BellReset2**
  - Description:
  - Args:
  - Notes:

- **A_FSwordFlames**
  - Description:
  - Args:
  - Notes:

- **A_CStaffMissileSlither**
  - Description:
  - Args:
  - Notes:

- **A_CFlameRotate**
  - Description:
  - Args:
  - Notes:

- **A_CFlamePuff**
  - Description:
  - Args:
  - Notes:

- **A_CFlameMissile**
  - Description:
  - Args:
  - Notes:

- **A_CHolySeek**
  - Description:
  - Args:
  - Notes:

- **A_CHolyCheckScream**
  - Description:
  - Args:
  - Notes:

- **A_CHolyTail**
  - Description:
  - Args:
  - Notes:

- **A_CHolySpawnPuff**
  - Description:
  - Args:
  - Notes:

- **A_CHolyAttack2**
  - Description:
  - Args:
  - Notes:

- **A_LightningZap**
  - Description:
  - Args:
  - Notes:

- **A_LightningClip**
  - Description:
  - Args:
  - Notes:

- **A_LightningRemove**
  - Description:
  - Args:
  - Notes:

- **A_LastZap**
  - Description:
  - Args:
  - Notes:

- **A_ZapMimic**
  - Description:
  - Args:
  - Notes:

- **A_MStaffWeave**
  - Description:
  - Args:
  - Notes:

- **A_MStaffTrack**
  - Description:
  - Args:
  - Notes:

- **A_SnoutAttack**
  - Description:
  - Args:
  - Notes:

- **A_ShedShard**
  - Description:
  - Args:
  - Notes:

- **A_FreezeDeath**
  - Description:
  - Args:
  - Notes:

- **A_FreezeDeathChunks**
  - Description:
  - Args:
  - Notes:

- **A_SpeedFade**
  - Description:
  - Args:
  - Notes:

- **A_IceSetTics**
  - Description:
  - Args:
  - Notes:

- **A_IceCheckHeadDone**
  - Description:
  - Args:
  - Notes:

- **A_PigPain**
  - Description:
  - Args:
  - Notes:

- **A_PigLook**
  - Description:
  - Args:
  - Notes:

- **A_PigChase**
  - Description:
  - Args:
  - Notes:

- **A_PigAttack**
  - Description:
  - Args:
  - Notes:

- **A_QueueCorpse**
  - Description:
  - Args:
  - Notes:

- **A_CentaurAttack**
  - Description:
  - Args:
  - Notes:

- **A_CentaurAttack2**
  - Description:
  - Args:
  - Notes:

- **A_SetReflective**
  - Description:
  - Args:
  - Notes:

- **A_CentaurDefend**
  - Description:
  - Args:
  - Notes:

- **A_UnSetReflective**
  - Description:
  - Args:
  - Notes:

- **A_CentaurDropStuff**
  - Description:
  - Args:
  - Notes:

- **A_CheckFloor**
  - Description:
  - Args:
  - Notes:

- **A_DemonAttack1**
  - Description:
  - Args:
  - Notes:

- **A_DemonAttack2**
  - Description:
  - Args:
  - Notes:

- **A_DemonDeath**
  - Description:
  - Args:
  - Notes:

- **A_Demon2Death**
  - Description:
  - Args:
  - Notes:

- **A_WraithRaiseInit**
  - Description:
  - Args:
  - Notes:

- **A_WraithRaise**
  - Description:
  - Args:
  - Notes:

- **A_WraithInit**
  - Description:
  - Args:
  - Notes:

- **A_WraithLook**
  - Description:
  - Args:
  - Notes:

- **A_WraithChase**
  - Description:
  - Args:
  - Notes:

- **A_WraithFX3**
  - Description:
  - Args:
  - Notes:

- **A_WraithMelee**
  - Description:
  - Args:
  - Notes:

- **A_WraithMissile**
  - Description:
  - Args:
  - Notes:

- **A_WraithFX2**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurFade1**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurFade2**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurLook**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurChase**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurRoam**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk1**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurDecide**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk2**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurAtk3**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurCharge**
  - Description:
  - Args:
  - Notes:

- **A_SmokePuffExit**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurFade0**
  - Description:
  - Args:
  - Notes:

- **A_MntrFloorFire**
  - Description:
  - Args:
  - Notes:

- **A_SerpentChase**
  - Description:
  - Args:
  - Notes:

- **A_SerpentHumpDecide**
  - Description:
  - Args:
  - Notes:

- **A_SerpentUnHide**
  - Description:
  - Args:
  - Notes:

- **A_SerpentRaiseHump**
  - Description:
  - Args:
  - Notes:

- **A_SerpentLowerHump**
  - Description:
  - Args:
  - Notes:

- **A_SerpentHide**
  - Description:
  - Args:
  - Notes:

- **A_SerpentBirthScream**
  - Description:
  - Args:
  - Notes:

- **A_SetShootable**
  - Description:
  - Args:
  - Notes:

- **A_SerpentCheckForAttack**
  - Description:
  - Args:
  - Notes:

- **A_UnSetShootable**
  - Description:
  - Args:
  - Notes:

- **A_SerpentDiveSound**
  - Description:
  - Args:
  - Notes:

- **A_SerpentWalk**
  - Description:
  - Args:
  - Notes:

- **A_SerpentChooseAttack**
  - Description:
  - Args:
  - Notes:

- **A_SerpentMeleeAttack**
  - Description:
  - Args:
  - Notes:

- **A_SerpentMissileAttack**
  - Description:
  - Args:
  - Notes:

- **A_SerpentHeadPop**
  - Description:
  - Args:
  - Notes:

- **A_SerpentSpawnGibs**
  - Description:
  - Args:
  - Notes:

- **A_SerpentHeadCheck**
  - Description:
  - Args:
  - Notes:

- **A_FloatGib**
  - Description:
  - Args:
  - Notes:

- **A_DelayGib**
  - Description:
  - Args:
  - Notes:

- **A_SinkGib**
  - Description:
  - Args:
  - Notes:

- **A_BishopDecide**
  - Description:
  - Args:
  - Notes:

- **A_BishopDoBlur**
  - Description:
  - Args:
  - Notes:

- **A_BishopSpawnBlur**
  - Description:
  - Args:
  - Notes:

- **A_BishopChase**
  - Description:
  - Args:
  - Notes:

- **A_BishopAttack**
  - Description:
  - Args:
  - Notes:

- **A_BishopAttack2**
  - Description:
  - Args:
  - Notes:

- **A_BishopPainBlur**
  - Description:
  - Args:
  - Notes:

- **A_BishopPuff**
  - Description:
  - Args:
  - Notes:

- **A_SetAltShadow**
  - Description:
  - Args:
  - Notes:

- **A_BishopMissileWeave**
  - Description:
  - Args:
  - Notes:

- **A_BishopMissileSeek**
  - Description:
  - Args:
  - Notes:

- **A_DragonInitFlight**
  - Description:
  - Args:
  - Notes:

- **A_DragonFlap**
  - Description:
  - Args:
  - Notes:

- **A_DragonFlight**
  - Description:
  - Args:
  - Notes:

- **A_DragonAttack**
  - Description:
  - Args:
  - Notes:

- **A_DragonPain**
  - Description:
  - Args:
  - Notes:

- **A_DragonCheckCrash**
  - Description:
  - Args:
  - Notes:

- **A_DragonFX2**
  - Description:
  - Args:
  - Notes:

- **A_EttinAttack**
  - Description:
  - Args:
  - Notes:

- **A_DropMace**
  - Description:
  - Args:
  - Notes:

- **A_FiredRocks**
  - Description:
  - Args:
  - Notes:

- **A_UnSetInvulnerable**
  - Description:
  - Args:
  - Notes:

- **A_FiredChase**
  - Description:
  - Args:
  - Notes:

- **A_FiredAttack**
  - Description:
  - Args:
  - Notes:

- **A_FiredSplotch**
  - Description:
  - Args:
  - Notes:

- **A_SmBounce**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyLook**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyChase**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyAttack**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyDie**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyMissilePuff**
  - Description:
  - Args:
  - Notes:

- **A_IceGuyMissileExplode**
  - Description:
  - Args:
  - Notes:

- **A_ClassBossHealth**
  - Description:
  - Args:
  - Notes:

- **A_FastChase**
  - Description:
  - Args:
  - Notes:

- **A_FighterAttack**
  - Description:
  - Args:
  - Notes:

- **A_ClericAttack**
  - Description:
  - Args:
  - Notes:

- **A_MageAttack**
  - Description:
  - Args:
  - Notes:

- **A_SorcSpinBalls**
  - Description:
  - Args:
  - Notes:

- **A_SpeedBalls**
  - Description:
  - Args:
  - Notes:

- **A_SpawnFizzle**
  - Description:
  - Args:
  - Notes:

- **A_SorcBossAttack**
  - Description:
  - Args:
  - Notes:

- **A_SorcBallOrbit**
  - Description:
  - Args:
  - Notes:

- **A_SorcBallPop**
  - Description:
  - Args:
  - Notes:

- **A_BounceCheck**
  - Description:
  - Args:
  - Notes:

- **A_SorcFX1Seek**
  - Description:
  - Args:
  - Notes:

- **A_SorcFX2Split**
  - Description:
  - Args:
  - Notes:

- **A_SorcFX2Orbit**
  - Description:
  - Args:
  - Notes:

- **A_SorcererBishopEntry**
  - Description:
  - Args:
  - Notes:

- **A_SpawnBishop**
  - Description:
  - Args:
  - Notes:

- **A_SorcFX4Check**
  - Description:
  - Args:
  - Notes:

- **A_KoraxStep2**
  - Description:
  - Args:
  - Notes:

- **A_KoraxChase**
  - Description:
  - Args:
  - Notes:

- **A_KoraxStep**
  - Description:
  - Args:
  - Notes:

- **A_KoraxDecide**
  - Description:
  - Args:
  - Notes:

- **A_KoraxMissile**
  - Description:
  - Args:
  - Notes:

- **A_KoraxCommand**
  - Description:
  - Args:
  - Notes:

- **A_KoraxBonePop**
  - Description:
  - Args:
  - Notes:

- **A_KSpiritRoam**
  - Description:
  - Args:
  - Notes:

- **A_KBoltRaise**
  - Description:
  - Args:
  - Notes:

- **A_KBolt**
  - Description:
  - Args:
  - Notes:

- **A_BatSpawnInit**
  - Description:
  - Args:
  - Notes:

- **A_BatSpawn**
  - Description:
  - Args:
  - Notes:

- **A_BatMove**
  - Description:
  - Args:
  - Notes:

- **A_FlyTarget**
  - Description:
  - Args:
  - Notes:

- **A_FlyChase**
  - Description:
  - Args:
  - Notes:

## Actor Codepointer EX Overview

- **A_PlaySound**
  - Description:
  - Args:
  - Notes:

- **A_FaceTargetSound**
  - Description:
  - Args:
  - Notes:

- **A_RandomJump**
  - Description:
  - Args:
  - Notes:

- **A_SpawnObject**
  - Description:
  - Args:
  - Notes:

- **A_MonsterProjectile**
  - Description:
  - Args:
  - Notes:

- **A_MonsterBulletAttack**
  - Description:
  - Args:
  - Notes:

- **A_MonsterMeleeAttack**
  - Description:
  - Args:
  - Notes:

- **A_MonsterComboAttack**
  - Description:
  - Args:
  - Notes:

- **A_SpawnMinion**
  - Description:
  - Args:
  - Notes:

- **A_RadiusDamage**
  - Description:
  - Args:
  - Notes:

- **A_SeekTracer**
  - Description:
  - Args:
  - Notes:

- **A_FindTracer**
  - Description:
  - Args:
  - Notes:

- **A_ClearTracer**
  - Description:
  - Args:
  - Notes:

- **A_MissileExplode**
  - Description:
  - Args:
  - Notes:

- **A_MissileCountdown**
  - Description:
  - Args:
  - Notes:

- **A_DeathBallBounceEX**
  - Description:
  - Args:
  - Notes:

- **A_TempestChain**
  - Description:
  - Args:
  - Notes:

- **A_TempestSpray**
  - Description:
  - Args:
  - Notes:

- **A_KnightAttackEX**
  - Description:
  - Args:
  - Notes:

- **A_ClinkRushEX**
  - Description:
  - Args:
  - Notes:

- **A_ClinkAttackEX**
  - Description:
  - Args:
  - Notes:

- **A_MinotaurMeleeEX**
  - Description:
  - Args:
  - Notes:

- **A_TrollChargeStart**
  - Description:
  - Args:
  - Notes:

- **A_TrollCharge**
  - Description:
  - Args:
  - Notes:

- **A_SuperDemonAttack1**
  - Description:
  - Args:
  - Notes:

- **A_SuperDemonAttack2**
  - Description:
  - Args:
  - Notes:

- **A_ShedShardEX**
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardRaise**
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardHold**
  - Description:
  - Args:
  - Notes:

- **A_CentaurEXGuardLower**
  - Description:
  - Args:
  - Notes:

- **A_CentaurAttack2EX**
  - Description:
  - Args:
  - Notes:

## Weapon Codepointer Overview
- **A_Light0**
  - Description:
  - Args:
  - Notes:

- **A_Light1**
  - Description:
  - Args:
  - Notes:

- **A_Light2**
  - Description:
  - Args:
  - Notes:

- **A_WeaponReady**
  - Description:
  - Args:
  - Notes:

- **A_Lower**
  - Description:
  - Args:
  - Notes:

- **A_Raise**
  - Description:
  - Args:
  - Notes:

- **A_ReFire**
  - Description:
  - Args:
  - Notes:

- **A_StaffAttackPL1**
  - Description:
  - Args:
  - Notes:

- **A_StaffAttackPL2**
  - Description:
  - Args:
  - Notes:

- **A_BeakReady**
  - Description:
  - Args:
  - Notes:

- **A_BeakRaise**
  - Description:
  - Args:
  - Notes:

- **A_BeakAttackPL1**
  - Description:
  - Args:
  - Notes:

- **A_BeakAttackPL2**
  - Description:
  - Args:
  - Notes:

- **A_GauntletAttack**
  - Description:
  - Args:
  - Notes:

- **A_FireBlasterPL1**
  - Description:
  - Args:
  - Notes:

- **A_FireBlasterPL2**
  - Description:
  - Args:
  - Notes:

- **A_FireMacePL1**
  - Description:
  - Args:
  - Notes:

- **A_FireMacePL2**
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodPL1**
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodPL2**
  - Description:
  - Args:
  - Notes:

- **A_FireGoldWandPL1**
  - Description:
  - Args:
  - Notes:

- **A_FireGoldWandPL2**
  - Description:
  - Args:
  - Notes:

- **A_FirePhoenixPL1**
  - Description:
  - Args:
  - Notes:

- **A_InitPhoenixPL2**
  - Description:
  - Args:
  - Notes:

- **A_FirePhoenixPL2**
  - Description:
  - Args:
  - Notes:

- **A_ShutdownPhoenixPL2**
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowPL1**
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowPL2**
  - Description:
  - Args:
  - Notes:

- **A_FPunchAttack**
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckUp**
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckUpG**
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckReady**
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckReadyG**
  - Description:
  - Args:
  - Notes:

- **A_FAxeCheckAtk**
  - Description:
  - Args:
  - Notes:

- **A_FAxeAttack**
  - Description:
  - Args:
  - Notes:

- **A_SnoutAttack**
  - Description:
  - Args:
  - Notes:

- **A_FHammerAttack**
  - Description:
  - Args:
  - Notes:

- **A_FHammerThrow**
  - Description:
  - Args:
  - Notes:

- **A_FSwordAttack**
  - Description:
  - Args:
  - Notes:

- **A_CMaceAttack**
  - Description:
  - Args:
  - Notes:

- **A_CStaffInitBlink**
  - Description:
  - Args:
  - Notes:

- **A_CStaffCheckBlink**
  - Description:
  - Args:
  - Notes:

- **A_CStaffCheck**
  - Description:
  - Args:
  - Notes:

- **A_CStaffAttack**
  - Description:
  - Args:
  - Notes:

- **A_CFlameAttack**
  - Description:
  - Args:
  - Notes:

- **A_CHolyAttack**
  - Description:
  - Args:
  - Notes:

- **A_CHolyPalette**
  - Description:
  - Args:
  - Notes:

- **A_MWandAttack**
  - Description:
  - Args:
  - Notes:

- **A_LightningReady**
  - Description:
  - Args:
  - Notes:

- **A_MLightningAttack**
  - Description:
  - Args:
  - Notes:

- **A_MStaffAttack**
  - Description:
  - Args:
  - Notes:

- **A_MStaffPalette**
  - Description:
  - Args:
  - Notes:

- **A_FireConePL1**
  - Description:
  - Args:
  - Notes:

## Weapon Codepointer EX Overview

- **A_WeaponSound**
  - Description:
  - Args:
  - Notes:

- **A_WeaponProjectile**
  - Description:
  - Args:
  - Notes:

- **A_WeaponBulletAttack**
  - Description:
  - Args:
  - Notes:

- **A_WeaponMeleeAttack**
  - Description:
  - Args:
  - Notes:

- **A_ConsumeAmmo**
  - Description:
  - Args:
  - Notes:

- **A_GauntletAttackEX**
  - Description:
  - Args:
  - Notes:

- **A_FireGoldWandEX1**
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowEX1**
  - Description:
  - Args:
  - Notes:

- **A_FireCrossbowEX2**
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodEX1**
  - Description:
  - Args:
  - Notes:

- **A_FireSkullRodEX2**
  - Description:
  - Args:
  - Notes:

- **A_FirePhoenixEX2**
  - Description:
  - Args:
  - Notes:

- **A_FireMaceEX1**
  - Description:
  - Args:
  - Notes:

- **A_FireMaceEX2**
  - Description:
  - Args:
  - Notes:

- **A_FireTempestWandPL1**
  - Description:
  - Args:
  - Notes:

- **A_CShieldParry**
  - Description:
  - Args:
  - Notes:

- **A_CShieldGuard**
  - Description:
  - Args:
  - Notes:

- **A_CShieldLower**
  - Description:
  - Args:
  - Notes:

- **A_FireConeEX1**
  - Description:
  - Args:
  - Notes:

- **A_MStaffAttackEX**
  - Description:
  - Args:
  - Notes:

## Pickup Codepointer EX Overview
- **A_PickupHealth**
  - Description:
  - Args:
  - Notes:

- **A_PickupBasicArmor**
  - Description:
  - Args:
  - Notes:

- **A_PickupHexenArmor**
  - Description:
  - Args:
  - Notes:

- **A_PickupBackpack**
  - Description:
  - Args:
  - Notes:

- **A_PickupWeapon**
  - Description:
  - Args:
  - Notes:

- **A_PickupWeaponPiece**
  - Description:
  - Args:
  - Notes:

- **A_PickupAmmo**
  - Description:
  - Args:
  - Notes:

- **A_PickupDualAmmo**
  - Description:
  - Args:
  - Notes:

- **A_PickupPower**
  - Description:
  - Args:
  - Notes:

- **A_PickupArtifact**
  - Description:
  - Args:
  - Notes:

- **A_PickupKey**
  - Description:
  - Args:
  - Notes:

## Power Codepointer EX Overview

- **A_PowerInvulnerableStart**
  - Description:
  - Args:
  - Notes:

- **A_PowerInvulnerableTick**
  - Description:
  - Args:
  - Notes:

- **A_PowerInvulnerableEnd**
  - Description:
  - Args:
  - Notes:

- **A_PowerInvisibilityStart**
  - Description:
  - Args:
  - Notes:

- **A_PowerInvisibilityEnd**
  - Description:
  - Args:
  - Notes:

- **A_PowerStrengthStart**
  - Description:
  - Args:
  - Notes:

- **A_PowerFlightStart**
  - Description:
  - Args:
  - Notes:

- **A_PowerFlightEnd**
  - Description:
  - Args:
  - Notes:

- **A_PowerWeaponLevel2Start**
  - Description:
  - Args:
  - Notes:

- **A_PowerWeaponLevel2End**
  - Description:
  - Args:
  - Notes:

## Artifact Codepointer EX Overview

- **A_ArtiGivePower**
  - Description:
  - Args:
  - Notes:

- **A_ArtiHealth**
  - Description:
  - Args:
  - Notes:

- **A_ArtiEgg**
  - Description:
  - Args:
  - Notes:

- **A_ArtiSummon**
  - Description:
  - Args:
  - Notes:

- **A_ArtiTeleport**
  - Description:
  - Args:
  - Notes:

- **A_ArtiTeleportOther**
  - Description:
  - Args:
  - Notes:

- **A_ArtiBoostMana**
  - Description:
  - Args:
  - Notes:

- **A_ArtiBoostArmor**
  - Description:
  - Args:
  - Notes:

- **A_ArtiBlastRadius**
  - Description:
  - Args:
  - Notes:

- **A_ArtiFlechette**
  - Description:
  - Args:
  - Notes:

- **A_ArtiThrowingBomb**
  - Description:
  - Args:
  - Notes:

- **A_ArtiThrowingBombEX**
  - Description:
  - Args:
  - Notes:

- **A_ArtiPoisonBag**
  - Description:
  - Args:
  - Notes:

- **A_ArtiFireBomb**
  - Description:
  - Args:
  - Notes:

- **A_ArtiHealingRadius**
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveArmor**
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveArmorEX**
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveHealth**
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveMana**
  - Description:
  - Args:
  - Notes:

- **A_RadiusGiveManaEX**
  - Description:
  - Args:
  - Notes:

- **A_ArtiPuzzleItem**
  - Description:
  - Args:
  - Notes:
