---
layout: post
title: Changing items' and buildings' properties with statBases
excerpt: What makes every building, apparel or weapon special? Cost, quality, beauty, medical outcomes, and so on in examples with explanations.
date: 2021-08-05
updatedDate: 2021-08-05
tags:
    - xml
    - defs
    - post
    - reference
---

### Universal statBases

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your building. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_YourBuilding</defName>
        <!-- How this building is called in the game -->
        <label>tailor mechabench</label>
        <!-- ... -->

        <statBases>
            <!-- Basic stats -->
            <Mass>30</Mass>
            <MaxHitPoints>150</MaxHitPoints>
            <!-- Affects the speed at which flame spreads. Doesn't affect damage received from flame. -->
            <!-- 0.0 is inflammable, 0.2 is steel, 1.0 is wood. Can be anything in-between and even higher than 1. -->
            <Flammability>0.75</Flammability>
            <!-- Required for raw resources or usually unobtainable items (like a vanometric cell) -->
            <!-- This value is usually computed automatically for manufactured items, but you can still set a fixed value here. -->
            <MarketValue>1500</MarketValue>
            <!-- Makes selling this item less profitable -->
            <SellPriceFactor>0.70</SellPriceFactor>

            <!-- Deterioration: HPs per day -->
            <DeteriorationRate>2</DeteriorationRate>

            <!-- Number of ticks to build (divide by 60 to convert into seconds) -->
            <!-- Setting this to 0 and not specifying the cost list will make the building
                 appear similar to crafting spots or sleeping spots -->
            <WorkToBuild>2500</WorkToBuild>

            <!-- Make the room dirtier (negaive) or cleaner (positive values) -->
            <!-- ⚠ Stackable items will multiply this effect -->
            <Cleanliness>-5</Cleanliness>

        </statBases>
    </ThingDef>

</Defs>
```

### Buildings' Defs

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your building. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_YourFoodItem</defName>
        <!-- How this building is called in the game -->
        <label>yummy sausage</label>
        <!-- ... -->

        <statBases>
            <!-- Bench work efficiency -->
            <WorkTableWorkSpeedFactor>0.9</WorkTableWorkSpeedFactor>

            <!-- Furniture -->
            <Comfort>0.4</Comfort>
            <BedRestEffectiveness>1</BedRestEffectiveness>
            <Beauty>10</Beauty>

            <!-- Recreation effectiveness; the bigger, the better -->
            <JoyGainFactor>1</JoyGainFactor>

            <!-- For research benches -->
            <ResearchSpeedFactor>0.75</ResearchSpeedFactor>

            <!-- The larger the value, the faster it opens. 1.0 is a steel door, 1.2 is a wooden door -->
            <!-- If the door is stuffable, then the value stacks with the used material's stat. -->
            <!-- E.g. 1.15 * 1.2 (wood) is 1.38 -->
            <DoorOpenSpeed>1.15</DoorOpenSpeed>

            <!-- Accuracy of a turret. -->
            <!-- 89% is Shooting lvl 0, 96% is the base level (lvl 8), 99% is lvl 20 -->
            <!-- Chance to hit a cell at distance a with the accuracy S is Sª -->
            <ShootingAccuracyTurret>0.96</ShootingAccuracyTurret>
            <!-- This multiplies with the accuracy of a weapon (defined in a separate def): -->
            <!--
                <AccuracyTouch>0.70</AccuracyTouch> Distance of 3 cells or less
                <AccuracyShort>0.64</AccuracyShort> 12-24
                <AccuracyMedium>0.41</AccuracyMedium> 25-39
                <AccuracyLong>0.22</AccuracyLong> 40+ cells
            -->

            <RangedWeapon_Cooldown>4.0</RangedWeapon_Cooldown>

            <TrapMeleeDamage>100</TrapMeleeDamage>
            <TrapSpringChance>1.0</TrapSpringChance>


            <!-- Medical outcomes -->
            <ImmunityGainSpeedFactor>1.07</ImmunityGainSpeedFactor>
            <SurgerySuccessChanceFactor>1</SurgerySuccessChanceFactor>
            <!-- Adds a flat bonus to tend quality -->
            <MedicalTendQualityOffset>0.10</MedicalTendQualityOffset>

            <!-- Royalty; adds a flat bonus to meditation focus -->
            <!-- Needs a CompProperties_MeditationFocus comp to work -->
            <MeditationFocusStrength>0.0</MeditationFocusStrength>

            <!-- Ideology stat for decorations -->
            <StyleDominance MayRequire="Ludeon.RimWorld.Ideology">10</StyleDominance>
        </statBases>
    </ThingDef>

</Defs>
```

### Food Defs

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your food. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_YourFoodItem</defName>
        <!-- How this food is called in the game -->
        <label>yummy sausage</label>
        <!-- ... -->

        <statBases>
            <!-- 1.0 is a simple meal. 1.6 is consumed by a human daily. Doesn't quite match together, eh? -->
            <Nutrition>0.9</Nutrition>
            <!-- 0.05 is a corpse, 0.02 is a raw item, 0.96 is a meal from lvl0 cook in an area littered with corpses -->
            <FoodPoisonChanceFixedHuman>0.02</FoodPoisonChanceFixedHuman>
        </statBases>
    </ThingDef>

</Defs>
```

### Material Defs (for the Stuff)

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your material. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_Adamantium</defName>
        <!-- How this material is called in the game -->
        <label>adamantium</label>
        <!-- ... -->

        <statBases>
            <!-- 0.81 is 81% in-game -->
            <!-- Note that the following values stack with the properties of apparel/building/whatever. -->
            <!-- For apparel, the item's modifier is usually lower than 0.5, so your values are at least halved. -->
            <!-- Plus it gets affected by quality. -->
            <StuffPower_Armor_Sharp>0.81</StuffPower_Armor_Sharp>
            <StuffPower_Armor_Blunt>0.24</StuffPower_Armor_Blunt>
            <StuffPower_Armor_Heat>1.5</StuffPower_Armor_Heat>
            <!-- In degrees -->
            <StuffPower_Insulation_Cold>16</StuffPower_Insulation_Cold>
            <StuffPower_Insulation_Heat>16</StuffPower_Insulation_Heat>
            <!-- These affect the damage dealt with a weapon made of this material. -->
            <SharpDamageMultiplier>0.85</SharpDamageMultiplier>
            <BluntDamageMultiplier>1.0</BluntDamageMultiplier>
        </statBases>
    </ThingDef>

</Defs>
```

### Apparel Defs

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your apparel. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_Whatever</defName>
        <!-- How this apparel is called in the game -->
        <label>whatever</label>
        <!-- ... -->

        <statBases>
            <!-- All these must be pretty self-explanatory. These are used with stuffed apparel. -->
            <!-- See info about materials' StuffPower properties above  as they stack on top of these values -->
            <StuffEffectMultiplierArmor>0.2</StuffEffectMultiplierArmor>
            <StuffEffectMultiplierInsulation_Cold>0.1</StuffEffectMultiplierInsulation_Cold>
            <StuffEffectMultiplierInsulation_Heat>0.15</StuffEffectMultiplierInsulation_Heat>

            <!-- These are used with non-stuffed apparel with fixed recipes. They stack with quality modifier only. -->
            <ArmorRating_Sharp>0.40</ArmorRating_Sharp>
            <ArmorRating_Blunt>0.08</ArmorRating_Blunt>
            <ArmorRating_Heat>0.10</ArmorRating_Heat>
            <Insulation_Cold>14.4</Insulation_Cold>
            <Insulation_Heat>3</Insulation_Heat>

            <EquipDelay>1.5</EquipDelay>
        </statBases>
    </ThingDef>

</Defs>
```

### Misc.

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>

    <ThingDef ParentName="BenchBase">
        <!-- The name of your material. This should be unique. The name is not shown to players. -->
        <defName>YOURMOD_Whatever</defName>
        <!-- How this material is called in the game -->
        <label>whatever</label>
        <!-- ... -->

        <statBases>
            <!-- Requires <thingClass>ShieldBelt</thingClass> -->
            <EnergyShieldRechargeRate>0.13</EnergyShieldRechargeRate>
            <EnergyShieldEnergyMax>1.1</EnergyShieldEnergyMax>
        </statBases>
    </ThingDef>

</Defs>
```