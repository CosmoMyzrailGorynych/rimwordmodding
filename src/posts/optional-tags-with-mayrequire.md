---
layout: post
title: Optional tags with MayRequire
excerpt: A new v1.3 XML feature that brings an alternative for XML Patches when you want to provide a support for an optional mod.
date: 2021-08-08
updatedDate: 2021-08-08
tags:
    - xml
    - defs
    - post
---
So as I'd been updating my [Nutrition](https://steamcommunity.com/sharedfiles/filedetails/?id=2546863371) mod with support for modded meals, I wanted to disallow cooking gourmet and lavish meals for a specific, preindustrial-tech portable stove. Gourmet meals, in particular, come from the Vanilla Cooking Expanded mod, plus its own Stew expansion. A lot of recipes, actually. And I didn't want to write patches, because it is tedious, and I also knew about that new tag I glanced in Ideology stats and attributes that are present in vanilla defs.

Shortly, that's how my list of disallowed recipes looked like:

```xml
<!-- This tag uses a custom-written mod def extension and is not of a vanilla API -->
<disallowedRecipes>
    <!-- Vanilla recipes -->
    <li>CookMealLavish</li>
    <li>CookMealLavish_Veg</li>
    <li>CookMealLavish_Meat</li>
    <li>CookMealSurvival</li>
    <li>CookMealLavishBulk</li>
    <li>CookMealLavishBulk_Veg</li>
    <li>CookMealLavishBulk_Meat</li>
    <li>CookMealSurvivalBulk</li>
    <!-- Mod "Vanilla Cooking Expanded" -->
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeLavish</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeLavishBulk</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeGourmet</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertLavish</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertLavishBulk</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertGourmet</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookGrillLavish</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookGrillLavishhBulk</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookGrillGourmet</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookMealGourmet</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookSoupLavish</li>
    <li MayRequire="VanillaExpanded.VCookE">VCE_CookSoupGourmet</li>
    <!-- Mod "Vanilla Cooking Expanded: Stews" -->
    <li MayRequire="VanillaExpanded.VCookEStews">VCE_CookStewLavish</li>
</disallowedRecipes>
```

`VanillaExpanded.VCookE` and `VanillaExpanded.VCookEStews` are `packageId`s of the corresponding mods. You can find it in mods' `About.xml` file.

Without this attribute, users would get loading errors for the bench, if a user hadn't the needed mods. The attribute seems to work not just on recipes but on an at least broad variety of tags, if not on every one of them.

For example, you can do the opposite: specify a list of allowed optional recipes using vanilla XML features:

```xml
<?xml version="1.0" encoding="utf-8"?>
<Defs>
    <ThingDef ParentName="BenchBase">
        <defName>COMIGO_PortableChemfuelStove</defName>
        <label>portable chemfuel stove</label>
        <!-- Irrelevant here but required for Rimword tags were stripped out,
             so do not use the whole code as is. -->
        <recipes>
            <li>CookMealSimple</li>
            <li>CookMealFine</li>
            <li>CookMealFine_Veg</li>
            <li>CookMealFine_Meat</li>
            <li>CookMealSimpleBulk</li>
            <li>CookMealFineBulk</li>
            <li>CookMealFineBulk_Veg</li>
            <li>CookMealFineBulk_Meat</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeLavish</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeLavishBulk</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookBakeGourmet</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertLavish</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertLavishBulk</li>
            <li MayRequire="VanillaExpanded.VCookE">VCE_CookDessertGourmet</li>
        </recipes>
    </ThingDef>
</Defs>
```

So, yep. Happy modding.