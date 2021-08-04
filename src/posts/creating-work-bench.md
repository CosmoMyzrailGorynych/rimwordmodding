---
layout: post
title: Creating a new workbench with XML in Rimworld
excerpt: Example of a simple bench with modified stats that works
date: 2021-08-05
updatedDate: 2021-08-05
tags:
    - xml
    - defs
    - buildings
    - post
    - tutorial
---

So, shortly,

* **What we will make:** a simple bench (like a stove) that uses vanilla recipes. Modded recipes are easy to add once you have them.
* **What we will need:** a `ThingDef` for the building itself and `WorkGiver`.
* **Optional:** `Verb`.

The `ThingDef` is needed to describe a buildable (or not) edifice. In fact, ThingDefs are used to describe almost everything on your game's map, even including dirt, but we will use a special class `Building_WorkTable_HeatPush` to make a workbench.

The `WorkGiver` is needed to tell RimWorld that this building can have tasks for colonists. Without the latter, there will be a bench but colonists will never use it, and can't be forced to do so. It also affects how jobs are prioritized in the colony.

### The `ThingDef`
Suggested location: `RimWorld/Mods/Your Mod/1.x/Defs/Buildings/Production/Building name.xml`

Grab this code, create a file in your mod folder, and start filling in your values. This code describes a simple stove of a smaller size.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>
    <!-- ParentName="BenchBase" tells to pick a vanilla template for workbenches.
         It allows skipping a good portion of generic values. -->
    <ThingDef ParentName="BenchBase">
        <!-- The name of your building. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_YourBuilding</defName>
        <!-- How this building is called in the game -->
        <label>your stove</label>
        <!-- In-game description -->
        <description>A compact electrically-powered stove with an attached countertop for preparing meals.</description>
        <!-- All workbenches seem to use this class, even those that don't actually push heat, e.g. stonecutting table. -->
        <thingClass>Building_WorkTable_HeatPush</thingClass>
        <!-- Collision size in cells -->
        <size>(2,1)</size>
        <graphicData>
            <!-- Path to a texture, without the `Textures` folder at the beginning. -->
            <texPath>Things/Building/Production/SmallerCookingStove</texPath>
            <!-- Tells to look for different textures for different rotation angles -->
            <graphicClass>Graphic_Multi</graphicClass>
            <!-- How large the displayed texture is drawn. Doesn't affect collisions. -->
            <drawSize>(2.5,1.5)</drawSize>
        </graphicData>

        <!-- The amount and type of materials needed to build this bench -->
        <costList>
            <Steel>75</Steel>
            <ComponentIndustrial>3</ComponentIndustrial>
        </costList>

        <!-- You can add this section to allow making a bench from different additional materials -->
        <costStuffCount>50</costStuffCount>
        <stuffCategories>
            <li>Metallic</li>
            <li>Woody</li>
        </stuffCategories>

        <altitudeLayer>Building</altitudeLayer>
        <!-- Cover effectiveness -->
        <fillPercent>0.5</fillPercent>
        <useHitPoints>True</useHitPoints>
        <statBases>
            <Mass>30</Mass>
            <MaxHitPoints>150</MaxHitPoints>
            <Flammability>0.75</Flammability>
            <WorkTableWorkSpeedFactor>1</WorkTableWorkSpeedFactor>
            <!-- Number of ticks to build (divide by 60 to convert into seconds) -->
            <!-- Setting this to 0 will make the building to appear similar to crafting spots or sleeping spots -->
            <WorkToBuild>2500</WorkToBuild>
            <!-- This value is usually computed automatically, but you can set a fixed value here -->
            <MarketValue>1500</MarketValue>
        </statBases>
        <!-- Put the bench into production tab in architect UI -->
        <designationCategory>Production</designationCategory>
        <passability>PassThroughOnly</passability>
        <pathCost>50</pathCost> <!-- Larger values slow pawns and animals -->
        <pathCostIgnoreRepeat>true</pathCostIgnoreRepeat> <!-- Stop slowing when walking on items of one time, e.g. walking through ditches or on tables -->
        <hasInteractionCell>True</hasInteractionCell>
        <!-- Tweak the position of the spot at which a colonist will work with the bench -->
        <interactionCellOffset>(1,0,-1)</interactionCellOffset>
        <surfaceType>Item</surfaceType>
        <constructionSkillPrerequisite>4</constructionSkillPrerequisite>
        <!-- The recipes this bench can make. Here is a shorter list of meal recipes: -->
        <recipes>
            <li>CookMealSimple</li>
            <li>CookMealFine</li>
        </recipes>
        <!-- Creates a tab with bills in player's UI -->
        <inspectorTabs>
            <li>ITab_Bills</li>
        </inspectorTabs>
        <comps>
            <!-- Makes a building an electricity consumer -->
            <li Class="CompProperties_Power">
                <compClass>CompPowerTrader</compClass>
                <shortCircuitInRain>true</shortCircuitInRain>
                <basePowerConsumption>300</basePowerConsumption> <!-- Watt -->
            </li>
            <!-- Allows turning off an electricity-powered building -->
            <li Class="CompProperties_Flickable" />
            <!-- Makes the building break sometimes and require a repair with a component -->
            <li Class="CompProperties_Breakdownable" />
            <!-- Statically pushes heat -->
            <li Class="CompProperties_HeatPusher">
                <!-- Pushes warmth when powered -->
                <compClass>CompHeatPusherPowered</compClass>
                <heatPerSecond>3</heatPerSecond>
            </li>
        </comps>
        <building>
            <!-- Does this count as a meal source? Affects in-game alerts and tips -->
            <isMealSource>true</isMealSource>
            <!-- This line tells that people get experience by completing bills -->
            <spawnedConceptLearnOpportunity>BillsTab</spawnedConceptLearnOpportunity>
            <!-- How much heat does the building add while working on a bill? -->
            <heatPerTickWhileWorking>0.10</heatPerTickWhileWorking>
        </building>
        <!-- Alerts and helpers for placement -->
        <placeWorkers>
            <!-- Forbid overlaps with other buildings' working spots -->
            <li>PlaceWorker_PreventInteractionSpotOverlap</li>
        </placeWorkers>
        <researchPrerequisites>
            <!-- A list of researches that is required to build this edifice -->
            <li>Electricity</li>
        </researchPrerequisites>
    </ThingDef>

</Defs>
```

What you should change:

* **Change `defName`.** As said in the code, the name should be unique, and must not overlap with vanilla or other names. Prefixing the defName with your nickname or such is a good practice. For example, all my defs begin with `COMIGO_`, and modder YAYO starts every defName with `YAYO_`.
* **`costList` tag**. See the list of available vanilla materials in [the cheatsheet](/post/vanilla-defs-cheatsheet/#materials). Change `costStuffCount` and `stuffCategories` as well. Vanilla categories are `Metallic`, `Woody`, `Stony`, `Fabric`, and `Leathery`.
  * You can remove one of the cost blocks: you can remove `costList`, or remove `costStuffCount` and `stuffCategories`.
* Fill in `recipes` list. It is usually the main reason of why you would like to create a bench. Again, vanilla recipes can be found in [the cheatsheet](/post/vanilla-defs-cheatsheet/#recipes).
* **List of research requirements** in the tag `researchPrerequisites`. You can find the list of vanilla research projects' names in [the cheat-sheet](/post/vanilla-defs-cheatsheet/#research). If you don't need any prerequisites so that it is available to any faction at any time, remove the whole tag `<researchPrerequisites> ... </researchPrerequisites>`.

This creates a bench in the Production tab, and you can add bills via specified recipes, but colonists still won't use it.

### The `WorkGiver`

Suggested location: `RimWorld/Mods/Your Mod/1.x/Defs/WorkGiverDefs/MyBenchWorkGiver.xml`

The base code is as the following:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <WorkGiverDef>
        <defName>YOURMOD_YourJob</defName>
        <label>cook</label>
        <giverClass>WorkGiver_DoBill</giverClass>
        <!-- The type of job from the in-game Work tab -->
        <workType>Cooking</workType>
        <!-- The higher — the more important the job is. -->
        <!-- Important jobs are picked before other low-priority ones. This can greatly affect gameplay, actually. -->
        <!-- For example, cooking at a stove (100) is more important than cooking at a campfire (95),
             and is more important than butchering (90). -->
        <!-- You can see vanilla WorkGivers at  -->
        <priorityInType>100</priorityInType>
        <fixedBillGiverDefs>
            <!-- Make sure it matches the name of the bench! -->
            <li>YOURMOD_YourBuilding</li>
        </fixedBillGiverDefs>
        <verb>cook</verb>
        <gerund>cooking at</gerund>
        <!-- Can't cook w/o hands -->
        <requiredCapacities>
            <li>Manipulation</li>
        </requiredCapacities>
        <!-- continue working on the bill after cooking one thing -->
        <prioritizeSustains>true</prioritizeSustains>
    </WorkGiverDef>

</Defs>
```

What you should change:

* **`defName`**.
* **`workType`**. This affects how the job is prioritized over other activities, and whether particular colonists can perform it. [See the cheatsheet](/post/vanilla-defs-cheatsheet/#worktypes) for the list of workType names.
* **Make sure the `fixedBillGiverDefs` precisely lists the defName of your bench.** Otherwise, it won't work.

Now save, relaunch, test. Ta-da! You have a working bench!

Further tutorials on textures, recipes and such coming SOON.