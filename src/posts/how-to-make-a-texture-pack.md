---
layout: post
title: How to make a texture pack
excerpt: Replace vanilla or modded textures and change the way Rimworld picks textures, if needed.
date: 2021-08-09
updatedDate: 2021-08-09
tags:
    - xml
    - defs
    - textures
    - patches
    - post
---
Changing textures in the game is, probably, the easiest way to mod them. The process usually consists of these steps:

1. [Create a new mod folder and its metadata](https://rimworldwiki.com/wiki/Modding_Tutorials/Mod_folder_structure).
1. Find the mod folder or the official datapack, and inside them seek for defs you want to retexture.
1. In defs, find the path at which textures are stored, and also which drawing class is used. The latter affects which textures to expect. For non-official mods, you can jump straight to the Textures folder and write down the paths to your targeted textures.
   * DevMode will also help to list all the textures' variations names.
2. Optionally, change drawing class to enable/disable randomization or cutout mode.
3. Draw the new textures (duh).
4. Put them into proper locations in your mods based on info obtained before.

Quick disclaimer, that also explains why it works:

> All the textures from vanilla and mods are virtually merged in one huge folder, and if names and paths of two or more textures match, the mod that loads the last wins, overwrites them.

So,

### Finding the correct mod and defs

* If you want to change the textures of vanilla Rimworld or Ideology/Royalty expansions, find the Rimword folder on your PC. With Steam, you can open your library, right-click Rimworld, click Properties, then click Local Files tab -> Browse. There you will find the Data folder, with subfolders Core, Ideology and Royalty corresponding to the base game and expansions.
* Mods that are installed locally (not throught Steam) can be found at the same Rimworld folder, but in the Mods folder, not Data one.
* Mods that are installed with steam are in your Steam library.
  * A generic location for a Steam library is `C:\Games\SteamLibrary`, but you really should know the better where it is. Inside, navigate to the folder `steamapps\workshop\content\294100`. You need exactly `294100` — this is the ID of Rimworld in the Steam store.
  * You will probably have a crapload of mods here that display as a list of number-named folders — to find the needed one, better [go to Steam Workshop for Rimworld](https://steamcommunity.com/app/294100/workshop/) and just search by the name of the mod. You will get an URL similar to `https://steamcommunity.com/sharedfiles/filedetails/?id=2545774148`, and here `2545774148` is the name of the mod's folder on your PC.

Then, the Defs. They are *usually* well categorized, but each modder does it in their own way, eventually.

* Most probably you want to tweak `ThingDefs` — the ones that are displayed on the map. These are stored in `RimWorld\Data\Core\Defs\` folder, with `ThingDefs_Buildings`, `ThingDefs_Plants`, `ThingDefs_Items`, `ThingDefs_Races`, `ThingDefs_Misc` inside it. You may as well need `TerrainDefs` to retexture floors.
* Mods usually follow a similar structure. Use your common sense. Go take a look inside those XML files, and use search to quicken things up.

### Understanding which textures are used

In a def, which is stored in an XML file, look for your thing's name, and find two tags: `texPath` and `graphicClass` inside the `graphicData` block.

For example, if I want to retexture that gross harp from Royalty, I navigate to `RimWorld\Data\Royalty\Defs\ThingDefs_Buildings` folder and open `Buildings_MusicalInstruments.xml`. Then I search for `Harp` word and quickly find its tag. Its' `texPath` is `Things/Building/Joy/harp_south` and `graphicClass` is `Graphic_Single`.

What does it give to us? I will write a more expanded article later, but here is the gist of these `graphicClass`es:

* `Graphic_Single` expects just one texture. The easiest scenario! The path to a texture is the retrieved `texPath` + `.png`, `Things/Building/Joy/harp_south.png` in our example.
* `Graphic_Multi` expects at least two textures for four orientations of a building. There can be at max four textures with suffixes `_south`, `_north`, `_east`, `_west`. For example, there are `FabricationBench_south.png`, `FabricationBench_north.png`, `FabricationBench_east.png`. Lacking textures will be replaced with mirrored opposite versions in-game.
* `Graphic_Random` is the most tricky. Its `texPath` points to a **folder**, not a texture file, and inside this folder you add several textures. A random one will be picked. For example, for `texPath` `Things/Plant/TreeOak` we may have `Things/Plant/TreeOak/TreeOakA.png`, `Things/Plant/TreeOak/TreeOakB.png`, `Things/Plant/TreeOak/TreeOakC.png`, and so on. Notice how we duplicate the name of the texture in our path.

> It seems that initially the harp had several textures in mind, but something was changed during the development, hence the `_south` suffix.

> How can you get the list of vanilla texture variations? Make sure that Development mode in Rimworld's options is on, and click the second "click" icon above your pawn's portraits. Search for the button "Loaded Assets", and press it. The list of all the loaded assets will be in the console (open and close with \` button). With a text search in another app, you will be able to find all the textures' names of the def you are interested in. For example, `TreeAnima` has five variations, up to `TreeAnimaE`.

Also see if there is `<shaderType>CutoutComplex</shaderType>`. This shader is used with stuffed items and instructs not to paint specific zones of a texture. It awaits additional textures — masks — and they have a suffix `m`. For example, you can have `SmallBrewBench_eastm.png`, `SmallBrewBench_northm.png`, `SmallBrewBench_southm.png`, `SmallBrewBench_westm.png`.

Okay, by now we should know which texture paths are expected. We can draw our textures and put them to the corresponding locations of our mod.

### Changing drawing class with a patch

Say that we have a plant that has randomized graphics (`<graphicClass>Graphic_Random</graphicClass>`), but our new textures are so complex that we in no way want to create variations. We can change the graphic class to `Graphic_Single`!

We can create an `.xml` file at your Mod folder -> Patches directory, e.g. `ChangeTreeGraphicClass.xml`. For the built-in content, we would use:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Patch>
    <Operation Class="PatchOperationReplace">
        <!-- Show errors if something goes wrong -->
        <success>Normal</success>
        <!-- Change defName="AB_AlienTree" to the one you need -->
        <xpath>/Defs/ThingDef[defName="AB_AlienTree"]/graphicData/graphicClass</xpath>
        <value>
            <graphicClass>Graphic_Single</graphicClass>
        </value>
    </Operation>
</Patch>
```

For mods or official expansions:
```xml
<?xml version="1.0" encoding="utf-8"?>
<Patch>
    <Operation Class="PatchOperationFindMod">
        <mods>
            <!-- This must be the precise name of the mod or expansion as it shows in your mod list -->
            <li>Alpha Biomes</li>
        </mods>
        <match Class="PatchOperationReplace">
            <!-- Show errors if something goes wrong -->
            <success>Normal</success>
            <!-- Change defName="AB_AlienTree" to the one you need -->
            <xpath>/Defs/ThingDef[defName="AB_AlienTree"]/graphicData/graphicClass</xpath>
            <value>
                <graphicClass>Graphic_Single</graphicClass>
            </value>
        </match>
    </Operation>
</Patch>
```

If you need to change a bunch of defs at once, you can edit the XPath and add the `or` statement:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Patch>
    <Operation Class="PatchOperationFindMod">
        <mods>
            <!-- This must be the precise name of the mod or expansion as it shows in your mod list -->
            <li>Alpha Biomes</li>
        </mods>
        <match Class="PatchOperationReplace">
            <!-- Show errors if something goes wrong -->
            <success>Normal</success>
            <!-- ⬇ Look how two defNames are specified here. You can specify more, too. -->
            <xpath>/Defs/ThingDef[defName="AB_AlienTree" or defName="AB_HalfAlienTree"]/graphicData/graphicClass</xpath>
            <value>
                <graphicClass>Graphic_Single</graphicClass>
            </value>
        </match>
    </Operation>
</Patch>
```

If you do change the `graphicClass`, remember that you will need to put your textures in new locations! See explanation of `graphicClass`es above.

### And that's it, actually!
With textures in correct places and optional patches, your mod will already work as intended. You may not need get the correct results on first try — but mistakes are usually inevitable, at least for humans. Read error messages that point to problematic places; with Patches, make sure your spelling is correct, including lowercase and uppercase letters.

If you still have problems, try asking in [the unofficial Rimworld's Discord server](https://discord.gg/rimworld).