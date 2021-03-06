# What We Know

## Server addresses
`prod.api.ascendgame.com` (confirmed)

`https://prod.api.ascendgame.com/users/me/id`

`gds4.steampowered.com`

`https://profile.xboxlive.com`

`1.214.68.2` (Unconfirmed)

## Technologies used
libogg.dll - Ogg Vorbis by Xiph.org

libtheora.dll - Theora by Xiph.org

libvorbis.dll - Vorbis by Xiph.org

iggy_w32.dll - Iggy by RAD Game Tools

Bzip/Gzip/LZMA

Deflate/Inflate (Inflate library used in-game)

zlib

OGG Video

SharpDX - SharpDX by Alexandre Mutel

AudioKinetic Wwise RIFF 

## Game Engine
Uses engine used in Toy Soldiers (and similar future PC releases from Signal Studios)

## .\*b format
`.oggb` and such files have weird file structure.

`.tgab` files have static 70(16) bytes of data and seems to be regular Targa image files.

`.nutb` files have F0(16) bytes of junk data.

`.xmlb` files have this header: `(4 byte header)!SigB!(02 00)(4 byte header)(80 bytes of null)`

Some files just have their custom header stuck in front of the file.

### .BNK and .WEM files
Using Wwise Audio Bank Extractor will extract some sort of audio data with `.wav` extension, but it's not playable yet. 

Seems to be either headless file or is using some sort of external compression. (Probably Deflate)

## QuickBMS script for unpacking data file (use command-line mode, not GUI mode)
```
goto 0x68
get FILES long
math OFF = 0x78
for i = 0 < FILES
goto OFF
get NAMEOFF THREEBYTE
math NAMEOFF -= 4
get DUMMY byte
get NAMESIZE long
get UNK1 long
get UNK2 long
get OFFSET long
get ZSIZE long
get SIZE long
getdstring DUMMY 0x1c
savepos OFF
goto NAMEOFF
getdstring NAME NAMESIZE
if SIZE == ZSIZE
log NAME OFFSET SIZE
else
clog NAME OFFSET ZSIZE SIZE
endif
next i
```
Command: `quickbms.exe (script file name) res.sacb (destination folder)`

## Game data found on memory
This game stores its filetable and other scripts on its memory.

Here is the filetable from `res.sacb`, at offset `114ED0`~`25744F`:

```
gameplay\spells\firespray\fire_spray.nutb
gui\textures\inventory\plate_01_helmet_var_02_g.pngb
art\characters\beast\highlands_wolverine\textures\highlands_wolverine_n.tgab
art\dungeons\random\sets\set_dark\pf_12_set_dark_breakdoor.0.geob
gui\textures\signboard\signboard_backshadow_g.pngb
art\characters\caos\hair\hair_09\caos_hair09.mshb
anims\environments\structures\stair_reveal\stair_reveal.anib
art\dungeons\custom\highlands\assets\dungeon_temple_drapery02_none.sigbgui\textures\messaging\how_to_combat_g.pngb
effects\metawar\totems\blessing_use_light.sigbart\characters\caos\armor\alignment\leather_03\leather_03_helm_light.0.geob
gameplay\player\player_ghost_ai.nutb
effects\objects\ability_altar\ability_altar_player_foot_dark.fxb
art\regions\highlands\shared\natural\water\textures\water_surface_02_void_d.tgab
effects\weapons\reaper.fxb
[...]
```

And this script from memory dump:
```
!SigB!
!SigB!
!SigB!
!SigB!
/////////////////////////////////////////////
//// ------ CommonGoal_AnimateUp - 0 ------
/////////////////////////////////////////////
class CommonGoal_AnimateUp extends AI.SigAIGoal
{
	params = 0 //user data
	params_ = 0 //construction data
	constructor( paramsIn )
	{
		params = paramsIn
		params_ = paramsIn
		AI.SigAIGoal.constructor( params )
		Setup( 0, "CommonGoal_AnimateUp", this )
	}
	function OnInsertion( goalDriven )
	{
		AI.SigAIGoal.OnInsertion( goalDriven )
		{
			if( ChildPriority( ) <= 0 )
			{
				PushGoal( /* AnimateUp */
					_6d545f8_3( params ) )
			}
		}
	}
	function DebugTypeName( )
		return "CommonGoal_AnimateUp - 0" 
}
/////////////////////////////////////////////
//// ------ CommonGoal_AnimateDown - 0 ------
/////////////////////////////////////////////
class CommonGoal_AnimateDown extends AI.SigAIGoal
{
	params = 0 //user data
	params_ = 0 //construction data
	constructor( paramsIn )
	{
		params = paramsIn
		params_ = paramsIn
		AI.SigAIGoal.constructor( params )
		Setup( 0, "CommonGoal_AnimateDown", this )
	}
	function OnInsertion( goalDriven )
	{
		AI.SigAIGoal.OnInsertion( goalDriven )
		{
			if( ChildPriority( ) <= 0 )
			{
				PushGoal( /* AnimateDown */
					_6d545f8_2( params ) )
			}
		}
	}
	function DebugTypeName( )
		return "CommonGoal_AnimateDown - 0" 
}
/////////////////////////////////////////////
//// ------ AnimateDown - 0 ------
/////////////////////////////////////////////
class _6d545f8_2 extends AI.SigAIGoal
{
	params = 0 //user data
	params_ = 0 //construction data
	constructor( paramsIn )
	{
		params = paramsIn
		params_ = paramsIn
		AI.SigAIGoal.constructor( params )
		Setup( 0, "_6d545f8_2", this )
	}
	function OnInsertion( goalDriven )
	{
		AI.SigAIGoal.OnInsertion( goalDriven )
		SetMotionState( "AnimateDown", params, false )
	}
	function DebugTypeName( )
		return "AnimateDown - 0" 
}
/////////////////////////////////////////////
//// ------ AnimateUp - 0 ------
/////////////////////////////////////////////
class _6d545f8_3 extends AI.SigAIGoal
{
	params = 0 //user data
	params_ = 0 //construction data
	constructor( paramsIn )
	{
		params = paramsIn
		params_ = paramsIn
		AI.SigAIGoal.constructor( params )
		Setup( 0, "_6d545f8_3", this )
	}
	function OnInsertion( goalDriven )
	{
		AI.SigAIGoal.OnInsertion( goalDriven )
		SetMotionState( "AnimateUp", params, false )
	}
	function DebugTypeName( )
		return "AnimateUp - 0" 
}
```

The complete filetable is included in this repo as `"res.sacb.idx"`.

Memory dump will be cleaned up and included here.

Update: Memory string dump uploaded: Check `MEMDUMP_TRUNCATED.TXT`.

## The good news
The files are all on the local side! Server seems to be only used for auth and multiplayer components.

### The bad news
~~...But I don't know how to get past the error screen yet. :sob:~~

Unconfirmed fix: ` 0x0134BF36 - Patch to mov eax,1 RETN // IsSignInPending 0x0134BE21 - NOP // IsSignedInOnline 0x0134BE26 - NOP // IsSignedInOnline2 0x00E5BE26 - NOP // (Crash Fix)`

Find these addresses and apply these instructions.


Server test PHP code is at https://pastebin.com/UfM5VK7b
