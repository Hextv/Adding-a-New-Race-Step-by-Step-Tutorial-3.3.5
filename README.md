# Adding a New Race - Step by Step Tutorial (3.3.5)

This guide provides a complete, step-by-step walkthrough on how to add fully playable custom races to a World of Warcraft 3.3.5a server based on TrinityCore. It covers every part of the process, from modifying the core, to editing DBCs, updating the database, and finally enabling the races in the game client.

--> [Jump straight to the Tutorial](##The-Tutorial)

## Credits to

**Tuerny** - for the original guide on Wowcreador (https://wowcreador.com/threads/tutorial-creacion-razas-custom-3-3-5a-tc-parte-1-4-core.987/)

**Crxgiles** (Edd the Duck IN Storyland) - For the translation from Spanish to English (https://discord.com/channels/407664041016688662/1338242825405468732)

**[Hex](https://github.com/Hextv)** - For editing the guide and making the repo (https://github.com/Hextv/Adding-a-New-Race-Step-by-Step-Tutorial-3.3.5)

## Tools for the Tutorial

### [Visual Studio Community](https://visualstudio.microsoft.com/vs/community/) (Used)

### Binary to Decimal Converter
[Rapid Tables BtD Converter 1](https://www.rapidtables.com/convert/number/binary-to-decimal.html) (Used)

[es.convertbinary BtD Converter 2](https://www.rapidtables.com/convert/number/binary-to-decimal.html) (Optional)

### DBC Editor (Used)

[WDBX Editor](https://github.com/WowDevTools/WDBXEditor) (Used)

[Rekt's WDBX Editor](https://github.com/rektbyfaith/WDBX-Editor--WoW-Modding/releases/tag/Release) (Optional)

### Blp Viewer (Optional)

[BlpV](https://github.com/Hextv/BlpV) (Optional)

[Blp View](https://www.wowinterface.com/downloads/info16700-BLPView.html) (Optional)

### PNG To BLP Converter (Optional)

[BLPtoPNG Online Converter](https://blp-convert.haaxor1689.dev/) (Optional)

[BLPNG Converter](https://www.wowinterface.com/downloads/info22128-BLPNGConverter.html) (Optional)

## The Tutorial

### Hello everyone,

I am bringing back the custom race implementation tutorial for your servers in version WotLK 3.3.5. To do this, it’s worth clarifying what we mean by "custom" in this tutorial:

- On one hand, we have the **Goblin race**, which we call custom, but it is actually already integrated into the official core, and we only need to configure it to unlock it.
  
- On the other hand, we have the **Worgen race**, which we will also call custom, but this one is not included in the core, and we will have to create lines before compiling to implement it.

With that said, let’s proceed with the tutorial. The first thing we are going to do, as I mentioned, is modify the core:

---

### MODIFICATION OF THE CORE

This is the first step we need to understand to start creating custom races. I assume you know how to download the TrinityCore git and pass it through CMake. If not, there are guides on the forum for that.

#### Modification of the "SharedDefines.h" file
- Open the "TrinityCore.sln" file with Visual Studio.
- In the Solution Explorer, search for "shared" and click on "SharedDefines.h". You will see the following:

```cpp
// Race value is index in ChrRaces.dbc
// EnumUtils: DESCRIBE THIS
enum Races
{
    RACE_NONE               = 0,  // SKIP
    RACE_HUMAN              = 1,  // TITLE Human
    RACE_ORC                = 2,  // TITLE Orc
    RACE_DWARF              = 3,  // TITLE Dwarf
    RACE_NIGHTELF           = 4,  // TITLE Night Elf
    RACE_UNDEAD_PLAYER      = 5,  // TITLE Undead
    RACE_TAUREN             = 6,  // TITLE Tauren
    RACE_GNOME              = 7,  // TITLE Gnome
    RACE_TROLL              = 8,  // TITLE Troll
    //RACE_GOBLIN           = 9,
    RACE_BLOODELF           = 10, // TITLE Blood Elf
    RACE_DRAENEI            = 11, // TITLE Draenei
    //RACE_FEL_ORC            = 12,
    //RACE_NAGA               = 13,
    //RACE_BROKEN             = 14,
    //RACE_SKELETON           = 15,
    //RACE_VRYKUL             = 16,
    //RACE_TUSKARR            = 17,
    //RACE_FOREST_TROLL       = 18,
    //RACE_TAUNKA             = 19,
    //RACE_NORTHREND_SKELETON = 20,
    //RACE_ICE_TROLL          = 21,
};
```

This is the section where we declare the IDs of the races we are going to use. If you look closely, we have the Goblin, but not the Worgen. To enable the races we want, we will do the following:

```cpp
// Race value is index in ChrRaces.dbc
// EnumUtils: DESCRIBE THIS
enum Races
{
    RACE_NONE               = 0,  // SKIP
    RACE_HUMAN              = 1,  // TITLE Human
    RACE_ORC                = 2,  // TITLE Orc
    RACE_DWARF              = 3,  // TITLE Dwarf
    RACE_NIGHTELF           = 4,  // TITLE Night Elf
    RACE_UNDEAD_PLAYER      = 5,  // TITLE Undead
    RACE_TAUREN             = 6,  // TITLE Tauren
    RACE_GNOME              = 7,  // TITLE Gnome
    RACE_TROLL              = 8,  // TITLE Troll
    //RACE_GOBLINOLD          = 9,
    RACE_BLOODELF           = 10, // TITLE Blood Elf
    RACE_DRAENEI            = 11, // TITLE Draenei
    //RACE_FEL_ORC            = 12,
    //RACE_NAGA               = 13,
    //RACE_BROKEN             = 14,
    //RACE_SKELETON           = 15,
    //RACE_VRYKUL             = 16,
    //RACE_TUSKARR            = 17,
    //RACE_FOREST_TROLL       = 18,
    //RACE_TAUNKA             = 19,
    //RACE_NORTHREND_SKELETON = 20,
    //RACE_ICE_TROLL          = 21,
    RACE_GOBLIN             = 23,
    RACE_WORGEN             = 24
};
```

Note that the Goblins, which were already declared but not accessible or playable, have been renamed to declare the new Goblin with ID 23. Personally, I don’t like leaving open roots declared in high IDs because it can cause errors when assigning languages, racials, and other things. That’s why I replace the ID of one race with another, like this:

```cpp
// Race value is index in ChrRaces.dbc
// EnumUtils: DESCRIBE THIS
enum Races
{
    RACE_NONE               = 0,  // SKIP
    RACE_HUMAN              = 1,  // TITLE Human
    RACE_ORC                = 2,  // TITLE Orc
    RACE_DWARF              = 3,  // TITLE Dwarf
    RACE_NIGHTELF           = 4,  // TITLE Night Elf
    RACE_UNDEAD_PLAYER      = 5,  // TITLE Undead
    RACE_TAUREN             = 6,  // TITLE Tauren
    RACE_GNOME              = 7,  // TITLE Gnome
    RACE_TROLL              = 8,  // TITLE Troll
    //RACE_GOBLINOLD          = 23,
    RACE_BLOODELF           = 10, // TITLE Blood Elf
    RACE_DRAENEI            = 11, // TITLE Draenei
    //RACE_FEL_ORC            = 24,
    //RACE_NAGA               = 13,
    //RACE_BROKEN             = 14,
    //RACE_SKELETON           = 15,
    //RACE_VRYKUL             = 16,
    //RACE_TUSKARR            = 17,
    //RACE_FOREST_TROLL       = 18,
    //RACE_TAUNKA             = 19,
    //RACE_NORTHREND_SKELETON = 20,
    //RACE_ICE_TROLL          = 21,
    RACE_GOBLIN             = 9,
    RACE_WORGEN             = 12
};
```

I have replaced `GOBLINOLD` with `GOBLIN` and `FEL_ORC` with `WORGEN`.

- Now let’s declare the maximum value of races. We must always assign the value of the highest open race ID and add 1:

This is what we have:

```cpp
// max+1 for player race
#define MAX_RACES         12
```

And this is what we will put, considering the above. The ID of the highest open race (we have Worgen = 12) and we add 1 (Worgen + 1 = 13):

```cpp
// max+1 for player race
#define MAX_RACES         13
```

- Now let’s define which races will be playable. To do this, we will see the following:

```cpp
#define RACEMASK_ALL_PLAYABLE \
    ((1<<(RACE_HUMAN-1)) | (1<<(RACE_ORC-1)) | (1<<(RACE_DWARF-1)) |\
     (1<<(RACE_NIGHTELF-1)) | (1<<(RACE_UNDEAD_PLAYER-1)) | (1<<(RACE_TAUREN-1)) |\
     (1<<(RACE_GNOME-1)) | (1<<(RACE_TROLL-1)) | (1<<(RACE_BLOODELF-1))|\
     (1<<(RACE_DRAENEI-1)))
```

To define them, we will do the following, taking the rule that every 3 roots we mark the end of the line with `\`, otherwise it will give you an error when compiling. So we put it this way:

```cpp
#define RACEMASK_ALL_PLAYABLE \
    ((1<<(RACE_HUMAN-1)) | (1<<(RACE_ORC-1)) | (1<<(RACE_DWARF-1)) |\
     (1<<(RACE_NIGHTELF-1)) | (1<<(RACE_UNDEAD_PLAYER-1)) | (1<<(RACE_TAUREN-1)) |\
     (1<<(RACE_GNOME-1)) | (1<<(RACE_TROLL-1)) | (1<<(RACE_WORGEN-1)) |\
     (1<<(RACE_BLOODELF-1)) | (1<<(RACE_DRAENEI-1)) | (1<<(RACE_GOBLIN-1)))
```

- The next thing is to define the faction, whether it’s Horde or Alliance. For this, we look for this:

```cpp
#define RACEMASK_ALLIANCE \
    ((1<<(RACE_HUMAN-1)) | (1<<(RACE_DWARF-1)) | (1<<(RACE_NIGHTELF-1)) | \
     (1<<(RACE_GNOME-1)) | (1<<(RACE_DRAENEI-1)))
```

And we declare the Worgen race for the Alliance, following the rule of the previous step (whatever you don’t declare there will be automatically assigned to the Horde):

```cpp
#define RACEMASK_ALLIANCE \
    ((1<<(RACE_HUMAN-1)) | (1<<(RACE_DWARF-1)) | (1<<(RACE_NIGHTELF-1)) | \
     (1<<(RACE_GNOME-1)) | (1<<(RACE_WORGEN-1)) | (1<<(RACE_DRAENEI-1)))
```

- With this, we will have finished configuring our precompile. Now we just need to mark the solution as Release and compile.

We will continue with part 2, where we will see the .dbc files that we need to configure.

---

### Part 2: Modifying DBC Files

In this second part, we are going to see which .dbc files we need to modify to make our races selectable.

#### Where do I get the DBCs from?

To get the correct .dbc files for our server, we need to go to our client and its official patches. We will always look for .dbc files in the patches with the greatest expansion to the least. That is, if I have "patch-3.MPQ" and "patch-2.MPQ", I extract from the major (patch-3.MPQ) and, if you can’t find it in that patch, go down: patch-2.MPQ ... patch.MPQ ...

Let me tell you that you will find almost all of them between the "patch-esES-3" and "patch-esES-2" patches (my client is Spanish).

Now, I will name the .dbc files that you should extract before starting:

- `ChrRaces.dbc`
- `CharBaseInfo.dbc`
- `CharStartOutfit.dbc`
- `SkillLineAbility.dbc`
- `SkillRaceClassInfo.dbc`
- `CreatureModelData.dbc`
- `CreatureDisplayInfo.dbc`
- `CreatureDisplayInfoExtra.dbc`
- `CharSections.dbc`
- `CharacterFacialHairStyles.dbc`
- `CharHairGeosets.dbc`

I will explain what you need to do in each .dbc file so that you understand it better.

---

### ChrRaces.dbc

In this .dbc, the races are defined as playable, the ID we assigned in the precompile, the faction, the display, etc. To understand it better, let’s visit `ChrRaces.dbc`.

We will take the Goblin and Fel_Orc lines and modify them like this:

```cpp
23,1,1,0x0,6894,6895,"Go",7,7,15007,0x448,"GoblinOld",0,0x2,"GoblinOld",,,,,,,,,,,,,,,,0xFF01FE,,,,,,,,,,,,,,,,,0xFF01EC,,,,,,,,,,,,,,,,,0xFF01EC,"NORMAL","NONE","NORMAL",0,
24,5,1,0x0,16981,16980,"Fo",7,7,15007,0x448,"FelOrc",0,0x2,"Fel Orc",,,,,,,,,,,,,,,,0xFF01FE,,,,,,,,,,,,,,,,,0xFF01EC,,,,,,,,,,,,,,,,,0xFF01EC,"NORMAL","NORMAL","NORMAL",0,
```

And we will add the custom races that we declared in part 1 of the tutorial:

```cpp
12,12,1,0x102C,40029,40030,"Ta",7,7,15007,0x448,"Worgen",81,0x0,"Worgen",,,,,,,,,,,,,,,,0xFF01FF,,,,,,,,,,,,,,,,,0xFF01EC,,,,,,,,,,,,,,,,,0xFF01EC,"NORMAL","HAIR","HORNS",0,
9,12,1,0x102D,40032,40033,"Go",1,7,15007,0x448,"Goblin",21,0x1,"Goblin",,,,,,,,,,,,,,,,0xFF01FF,,,,,,,,,,,,,,,,,0xFF01EC,,,,,,,,,,,,,,,,,0xFF01EC,"NORMAL","PIERCINGS","NORMAL",0,
```

---

### CharBaseInfo.dbc

This .dbc is responsible for relating race ID to class ID. We must add the custom races (in this case, we will only do the warrior) and assign them a class. So we do the following, we add this:

```cpp
9,1,
12,1,
```

---

### CharStartOutfit.dbc

This is where we declare the items that our custom races will use when we create them. What we have to do is add the following lines (remember that it is for warrior):

```cpp
380,9,1,0,0,38,39,40,0,49778,-1,-1,6948,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,9891,9892,10141,-1,2380,-1,-1,6418,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,4,7,8,-1,17,-1,-1,0,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
381,9,1,1,0,38,39,40,0,49778,-1,-1,6948,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,9891,9892,10141,-1,2380,-1,-1,6418,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,4,7,8,-1,17,-1,-1,0,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
382,12,1,0,0,6125,139,140,6948,49778,0,-1,0,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,9995,9988,9992,6418,22291,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,4,7,8,0,17,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
383,12,1,1,0,6125,139,140,6948,49778,0,-1,0,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,9995,9988,9992,6418,22291,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,4,7,8,0,17,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,-1,
```

---

### SkillLineAbility.dbc

This .dbc is responsible for assigning skills to characters. For example, to make them speak common language, orcish, etc. And that is what we are going to do with our new races, since without this, you will not be able to speak.

We are going to assign Skill 98, which is the common language, to Worgen. We are going to need a binary to decimal converter and vice versa. I use this website: [Binary to Decimal Converter](https://www.rapidtables.com/convert/number/binary-to-decimal.html).

We look for skill 98 (the skill IDs are in the second column) and we will see this:

```cpp
590,98,668,1101,0,,,1,0,0x2,0,0,,,
```

If we convert 1101 (which is what roots are allowed to use them) to binary with the converter, we will get `10001001101`. What does this mean? Take `1=ON`, `0=OFF` as a reference. And the order in which we have declared the races in part 1 of the tutorial according to their IDs. That is:

```
WORGEN | DRAENEI | BLOODELF | GOBLIN | TROLL | GNOME | TAUREN | UNDEAD_PLAYER | NIGHTELF | DWARF | ORC | HUMAN
```

So if we activate the common language for Alliance roots, including Worgen with ID 12, we will do the following:

```
1 | 1 | 0 | 0 | 0 | 1 | 0 | 0 | 1 | 1 | 0 |
```

The binary number we get is `110001001101`. Now we convert it to decimal with the converter, and it would give us `3149`.

With which we change the fourth column of skill 98 as follows:

```cpp
590,98,668,3149,0,,,1,0,0x2,0,0,,,
```

This also applies to the issue of assigning mesh skills, 2-handed axes, etc. Just look for the skill ID in Wowhead and modify it as I told you above to allow them in the roots you want.

---

### SkillRaceClassInfo.dbc

In this table, the information is assigned to the skill for the roots and classes you want. I am only going to touch on the third column, which is related to roots, as in the previous .dbc. The fourth column is the one related to classes, but I will leave it by default.

We look for the following line of skill 98:

```cpp
40,98,1101,1535,0x80,0x0,0,0x0,
```

And to change it, we need to do the same before converting to binary and then to decimal. But we already know the value, remember that it gave us `3149`, so we change it as follows:

```cpp
40,98,3149,1535,0x80,0x0,0,0x0,
```

We do the same with this other line for the Goblin, we search:

```cpp
48,109,690,1535,0x80,0x0,0,0x0,
```

And we change the value of the roots (third column):

```cpp
48,109,946,1535,0x80,0x0,0,0x0,
```

With this, your PCs will be able to speak correctly. Just like when we change values in `SkillRaceAbility.dbc`, we must also change them in this .dbc.

When you have several lines in a skill, as you have verified with 98 and 109, look at the fifth column; the value `0x80` means available to learn.

You can better understand this .dbc at this link: [SkillRaceClassInfo.dbc](https://wow.tools/dbc/?dbc=skillraceclassinfo).

---

### CreatureModelData.dbc

In this .dbc, we are going to assign the `.mdx` file of our race to both the male and female models for the races we are implementing. We do it by adding new lines:

```cpp
5005,2052,"Character\Worgen\Male\WorgenMale.mdx",1,1.0,1,7,18.0,12.0,1.0,,0,0,44,0.611100018024,2.03099989891,1.29404699802,-0.655966997147,-1.17623198032,0.000852000026498,0.702094972134,1.08035695553,2.29017710686,1.0,1.0,0.0,0.0,0.0,
5006,2052,"Character\Worgen\Female\WorgenFemale.mdx",1,1.0,1,7,18.0,12.0,1.0,,0,0,44,0.611100018024,2.03099989891,1.29404699802,-0.655966997147,-1.17623198032,0.000852000026498,0.702094972134,1.08035695553,2.29017710686,1.0,1.0,0.0,0.0,0.0,
5009,4,"Character\Goblin\Male\GoblinMale.mdx",1,1.0,1,1,12.0,10.0,1.0,,0,0,1128,0.555599987507,1.38900005817,0.460123002529,-0.452913999557,-0.579069972038,-0.0106370002031,0.470234006643,0.530076026917,1.39890694618,1.0,1.0,0.0,0.0,0.0,
5010,4,"Character\Goblin\Female\GoblinFemale.mdx",1,1.0,1,1,12.0,10.0,1.0,,0,0,1128,0.555599987507,1.38900005817,0.460123002529,-0.452913999557,-0.579069972038,-0.0106370002031,0.470234006643,0.530076026917,1.39890694618,1.0,1.0,0.0,0.0,0.0,
```

To know what each column is, visit: [CreatureModelData.dbc](https://wow.tools/dbc/?dbc=creaturemodeldata).

---

### CreatureDisplayInfo.dbc

In this .dbc, we are going to assign a DisplayID to our custom race model. To do this, we will put the following:

```cpp
40029,5005,0,25017,1.0,255,"","","","",3,0,0,0,0x0,0,
40030,5006,0,25018,1.0,255,"","","","",3,0,0,0,0x0,0,
40032,5009,0,25019,0.0,255,"","","","",0,0,0,0,0x0,0,
40033,5010,0,25020,0.0,255,"","","","",0,0,0,0,0x0,0,
```

Roughly speaking, this means that we created DisplayID 40029 for the CreatureModelData 5005 (WorgenMale) and with the CreatureDisplayInfoExtra 25017 that we will see now. We do that for each of the displays that we want to declare, which is why there are 4 lines, corresponding to the Male Worgen, Female Worgen, Male Goblin, and Female Goblin.

More information from the table at: [CreatureDisplayInfo.dbc](https://wow.tools/dbc/?dbc=creaturedisplayinfo).

---

### CreatureDisplayInfoExtra.dbc

In this .dbc, we are going to assign skins and other extras for the race. For this example, we will put the following in some new lines:

```cpp
25017,12,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,"CreatureDisplayExtra-21381.blp",
25018,12,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,"CreatureDisplayExtra-21381.blp",
25019,9,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,"CreatureDisplayExtra-21381.blp",
25020,9,1,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,0,1,"CreatureDisplayExtra-21381.blp",
```

---

### CharSections.dbc

In this .dbc, the paths to the `.blp` files for our custom races are established. It’s very easy; you just have to take the lines of a PC such as the Tauren, which is the one related to the placement of skins most similar to the Worgen. We paste them into a text editor like Notepad and rename the lines with high ID values and the path to the `.blp` we want to declare.

Here I leave you in a `.txt` the lines that you have to add to the .dbc: [DOWNLOAD](https://mega.nz/file/OExmyAgT#18nQxLxOSlSS9Ih1rbRnfgVbWPdO8UGlOPfHMCeMr34).

---

### CharacterFacialHairStyles.dbc

Here we will assign the values of the beards and facial hair for our races. In this case, the first thing would be to change the values that already come from the GoblinOld, remembering that by default it was ID=9, for the value ID=23, which is the one we gave it before, and that of the Fel_Orcs, which by default is 12, and we change it to 24. Once this is done, we add the following lines:

```cpp
12,0,0,0,0,0,0.0,2,
12,1,2,0,3,0,0.0,2,
12,1,3,0,4,0,0.0,2,
12,1,4,0,5,0,0.0,2,
12,1,5,0,6,0,0.0,2,
12,1,6,0,7,0,0.0,2,
12,1,7,0,8,0,0.0,2,
12,1,8,0,9,0,0.0,2,
12,1,9,0,10,0,0.0,2,
12,1,10,0,11,0,0.0,2,
12,0,1,2,0,0,0.0,2,
12,0,2,3,0,0,0.0,2,
12,0,3,4,0,0,0.0,2,
12,0,4,5,0,0,0.0,2,
12,0,5,6,0,0,0.0,2,
12,0,6,7,0,0,0.0,2,
12,0,7,8,0,0,0.0,2,
12,0,8,9,0,0,0.0,2,
12,0,9,10,0,0,0.0,2,
12,1,1,0,2,0,0.0,2,
9,1,0,6,11,2,0.0,0,
9,1,1,5,10,3,0.0,0,
9,1,2,2,9,4,0.0,0,
9,1,3,3,8,5,0.0,0,
9,1,4,2,7,6,0.0,0,
9,1,5,5,6,7,0.0,0,
9,1,6,4,5,8,0.0,0,
9,1,7,3,4,9,0.0,0,
9,1,8,2,3,10,0.0,0,
9,1,9,5,2,11,0.0,0,
9,1,10,4,11,12,0.0,0,
9,1,11,3,10,13,0.0,0,
9,0,0,6,11,2,0.0,0,
9,0,2,2,9,4,0.0,0,
9,0,3,3,8,5,0.0,0,
9,0,1,5,10,3,0.0,0,
9,0,8,2,3,10,0.0,0,
9,0,4,2,7,6,0.0,0,
9,0,5,5,6,7,0.0,0,
9,0,6,4,5,8,0.0,0,
9,0,7,3,4,9,0.0,0,
9,0,10,4,11,12,0.0,0,
9,0,9,5,2,11,0.0,0,
9,0,11,3,10,13,0.0,0,
9,0,13,6,8,3,0.0,0,
9,0,14,5,7,4,0.0,0,
9,0,18,6,3,8,0.0,0,
9,0,17,4,4,7,0.0,0,
9,0,16,3,5,6,0.0,0,
9,0,22,3,9,12,0.0,0,
9,0,24,2,7,13,0.0,0,
9,0,21,2,10,11,0.0,0,
9,0,19,5,2,9,0.0,0,
9,0,23,5,8,13,0.0,0,
9,0,20,4,11,10,0.0,0,
9,0,15,4,6,5,0.0,0,
9,0,12,5,9,2,0.0,0,
9,1,13,6,8,3,0.0,0,
9,1,12,5,9,2,0.0,0,
9,1,15,4,6,5,0.0,0,
9,1,14,5,7,4,0.0,0,
9,1,17,4,4,7,0.0,0,
9,1,16,3,5,6,0.0,0,
9,1,18,6,3,8,0.0,0,
9,1,23,5,8,13,0.0,0,
9,1,19,5,2,9,0.0,0,
```

More info about this .dbc at: [CharacterFacialHairStyles.dbc](https://wow.tools/dbc/?dbc=characterfacialhairstyles).

---

### CharHairGeosets.dbc

Here we will establish the geosets for our custom races. As we did previously, first we will replace all values 9 with 23 in the second column and values 12 with 24. Then we will add the following lines:

```cpp
536,9,0,3,4,0,
537,9,0,4,5,0,
538,9,1,2,4,0,
539,9,1,3,5,0,
540,9,1,4,6,0,
541,9,1,5,7,0,
542,9,1,6,8,0,
543,9,1,7,9,0,
544,9,1,8,10,0,
545,9,1,9,11,0,
546,9,1,10,12,0,
547,9,1,11,13,0,
548,9,1,12,14,0,
549,9,1,13,15,0,
550,9,1,14,16,0,
551,9,1,15,17,0,
552,9,1,16,18,0,
553,9,0,5,6,0,
554,9,0,6,7,0,
555,9,0,7,8,0,
556,9,0,9,10,0,
557,9,0,10,11,0,
558,9,0,8,9,0,
559,9,0,11,12,0,
560,9,0,12,13,0,
561,9,0,13,14,0,
562,9,0,14,15,0,
563,9,0,15,16,0,
564,9,0,16,17,0,
565,9,0,17,18,0,
566,9,0,2,3,0,
500,12,1,0,2,0,
501,12,1,3,5,0,
502,12,1,4,6,0,
503,12,1,5,7,0,
504,12,1,6,8,0,
505,12,1,7,9,0,
506,12,0,1,2,0,
507,12,1,1,3,0,
508,12,1,2,4,0,
509,12,0,1,2,0,
510,12,0,0,3,0,
511,12,0,3,5,0,
512,12,0,4,6,0,
519,12,1,8,10,0,
520,12,1,9,11,0,
521,12,1,14,16,0,
522,12,1,11,13,0,
523,12,1,12,14,0,
524,12,1,13,15,0,
525,12,1,15,17,0,
526,12,1,16,18,0,
527,12,1,17,19,0,
528,12,1,18,20,0,
529,12,1,19,21,0,
535,12,1,14,16,0,
```

With this, we will have completed our .dbc files for the custom races that we want to open.

To download the `.blp` files that I have mentioned, you can download them from here: [DOWNLOAD BLPs](https://mega.nz/file/KZxGBRxR#8ykFNi9MpnZXhZBWHSglp0-zil7tC1BqGoSKTm714NU). In them, you can find the necessary `.blp` files for male and female Goblin and for male and female Worgen.

Finally, we put all the modified .dbc files in a patch (that’s why there are guides in the forum) and then we also copy them to the "dbc" folder of our release.

If you think we’re done... I’ll tell you no, we still have to configure the database using SQL, and I’ll explain that in part 3.

---

### Part 3: Modifying the Database

For the third part of the tutorial, we will see the data that we must enter into the database through our manager. I use SQLYog to do it, and it works without problems. I have seen the SQL commands that I have used in the guide Goblin in this direction.

Without further ado, we will proceed to modify our database as follows:

#### Declare the Birthplace of Our Custom Races

Since Gilneas is not built into my server, I have placed the Worgen in the starting zone of humans and the Goblin in the starting zone of orcs. To do this, we put the following:

```sql
INSERT INTO `playercreateinfo` (`race`, `class`, `map`, `zone`, `position_x`, `position_y`, `position_z`, `orientation`) 
VALUES ('9', '1', '1', '14', '-618.518', '-4251.67', '38.718', '1');

INSERT INTO `playercreateinfo` (`race`, `class`, `map`, `zone`, `position_x`, `position_y`, `position_z`, `orientation`) 
VALUES ('12', '1', '0', '12', '-8949.95', '-132.493', '83.5312', '0');
```

#### Copy the Stats of Another Race

For the Goblin, we copy the stats of the Orc:

```sql
SET @NEW_RACE = 9; -- ID of the new race
SET @NEW_CLASS = 1; -- ID of the class you want
SET @COPY_RACE = 2; -- ID of the race you are copying the stats from

DELETE FROM `player_levelstats` WHERE race = @NEW_RACE AND class = @NEW_CLASS;

INSERT INTO `player_levelstats` (`race`, `class`, `level`, `str`, `agi`, `sta`, `inte`, `spi`)
SELECT @NEW_RACE, @NEW_CLASS, `level`, `str`, `agi`, `sta`, `inte`, `spi` 
FROM `player_levelstats`
WHERE race = @COPY_RACE AND class = @NEW_CLASS;
```

And for Worgen, we copy the stats of Humans:

```sql
SET @NEW_RACE = 12; -- ID of the new race
SET @NEW_CLASS = 1; -- ID of the class you want
SET @COPY_RACE = 1; -- ID of the race you are copying the stats from

DELETE FROM `player_levelstats` WHERE race = @NEW_RACE AND class = @NEW_CLASS;

INSERT INTO `player_levelstats` (`race`, `class`, `level`, `str`, `agi`, `sta`, `inte`, `spi`)
SELECT @NEW_RACE, @NEW_CLASS, `level`, `str`, `agi`, `sta`, `inte`, `spi` 
FROM `player_levelstats`
WHERE race = @COPY_RACE AND class = @NEW_CLASS;
```

#### Add Creation Information to Our Race

For the Goblin, we copy from the Orc again:

```sql
SET @NEW_RACE = 9;
SET @NEW_CLASS = 1;
SET @COPY_RACE = 2;

DELETE FROM `playercreateinfo_action` WHERE race = @NEW_RACE AND class = @NEW_CLASS;

INSERT INTO `playercreateinfo_action` (`race`, `class`, `button`, `action`, `type`)
SELECT @NEW_RACE, @NEW_CLASS, `button`, `action`, `type` 
FROM `playercreateinfo_action`
WHERE race = @COPY_RACE AND class = @NEW_CLASS;
```

And for Human Worgen:

```sql
SET @NEW_RACE = 12;
SET @NEW_CLASS = 1;
SET @COPY_RACE = 1;

DELETE FROM `playercreateinfo_action` WHERE race = @NEW_RACE AND class = @NEW_CLASS;

INSERT INTO `playercreateinfo_action` (`race`, `class`, `button`, `action`, `type`)
SELECT @NEW_RACE, @NEW_CLASS, `button`, `action`, `type` 
FROM `playercreateinfo_action`
WHERE race = @COPY_RACE AND class = @NEW_CLASS;
```

#### Modify the `playercreateinfo_skills` Table

Finally, we will have to go to the `playercreateinfo_skills` table and replace (remember part 2 of the tutorial) the decimal value of the races that are allowed the skill. Before, if you remember, we put skill 98 on the entire Alliance and skill 109 on the entire Horde.

Then we will look for those skills (in column 3) and modify the value `1101` to `3149` in skill 98 again and the value `690` to `946` in skill 109.

With this, we will have finished configuring our database. We close SQLYog and move on to part 4 of the guide to modify the Client Interface so that the new races appear in the creation panel.

---

### Part 4: Modifying the Client Interface

This will be the last part of the guide. After this, you will have successfully implemented the Worgens and Goblins on your server. Well, really, if you have already done the other 3 parts of the tutorial, you already have them implemented, but you still cannot access them from your client. For that, what we are going to do is change the interface files that refer to this and modify them so that our custom races appear in the character creation panel.

We are going to need several files for this, which, as I already explained in part 2, we will take from the patches from largest to smallest. The files to extract from the original MPQs are the following:

- `CharacterCreate.lua`
- `CharacterCreate.xml`
- `GlueParent.lua`
- `GlueStrings.lua`

Once we have them, we start the modifications.

---

### CharacterCreate.lua

Here we will establish the maximum number of races available. We will change this line:

```lua
MAX_RACES = 10;
```

To this (adding 2 more races):

```lua
MAX_RACES = 12;
```

Now we will establish the position of the race icons in their corresponding boxes. We search:

```lua
RACE_ICON_TCOORDS = {
    ["HUMAN_MALE"]        = {0, 0.125, 0, 0.25},
    ["DWARF_MALE"]        = {0.125, 0.25, 0, 0.25},
    ["GNOME_MALE"]        = {0.25, 0.375, 0, 0.25},
    ["NIGHTELF_MALE"]    = {0.375, 0.5, 0, 0.25},

    ["TAUREN_MALE"]        = {0, 0.125, 0.25, 0.5},
    ["SCOURGE_MALE"]    = {0.125, 0.25, 0.25, 0.5},
    ["TROLL_MALE"]        = {0.25, 0.375, 0.25, 0.5},
    ["ORC_MALE"]        = {0.375, 0.5, 0.25, 0.5},

    ["HUMAN_FEMALE"]    = {0, 0.125, 0.5, 0.75},
    ["DWARF_FEMALE"]    = {0.125, 0.25, 0.5, 0.75},
    ["GNOME_FEMALE"]    = {0.25, 0.375, 0.5, 0.75},
    ["NIGHTELF_FEMALE"]    = {0.375, 0.5, 0.5, 0.75},

    ["TAUREN_FEMALE"]    = {0, 0.125, 0.75, 1.0},
    ["SCOURGE_FEMALE"]    = {0.125, 0.25, 0.75, 1.0},
    ["TROLL_FEMALE"]    = {0.25, 0.375, 0.75, 1.0},
    ["ORC_FEMALE"]        = {0.375, 0.5, 0.75, 1.0},

    ["BLOODELF_MALE"]    = {0.5, 0.625, 0.25, 0.5},
    ["BLOODELF_FEMALE"]    = {0.5, 0.625, 0.75, 1.0},

    ["DRAENEI_MALE"]    = {0.5, 0.625, 0, 0.25},
    ["DRAENEI_FEMALE"]    = {0.5, 0.625, 0.5, 0.75},
};
```

And we add below the Draenei the icons of our new races:

```lua
RACE_ICON_TCOORDS = {
    ["HUMAN_MALE"]        = {0, 0.125, 0, 0.25},
    ["DWARF_MALE"]        = {0.125, 0.25, 0, 0.25},
    ["GNOME_MALE"]        = {0.25, 0.375, 0, 0.25},
    ["NIGHTELF_MALE"]    = {0.375, 0.5, 0, 0.25},

    ["TAUREN_MALE"]        = {0, 0.125, 0.25, 0.5},
    ["SCOURGE_MALE"]    = {0.125, 0.25, 0.25, 0.5},
    ["TROLL_MALE"]        = {0.25, 0.375, 0.25, 0.5},
    ["ORC_MALE"]        = {0.375, 0.5, 0.25, 0.5},

    ["HUMAN_FEMALE"]    = {0, 0.125, 0.5, 0.75},
    ["DWARF_FEMALE"]    = {0.125, 0.25, 0.5, 0.75},
    ["GNOME_FEMALE"]    = {0.25, 0.375, 0.5, 0.75},
    ["NIGHTELF_FEMALE"]    = {0.375, 0.5, 0.5, 0.75},

    ["TAUREN_FEMALE"]    = {0, 0.125, 0.75, 1.0},
    ["SCOURGE_FEMALE"]    = {0.125, 0.25, 0.75, 1.0},
    ["TROLL_FEMALE"]    = {0.25, 0.375, 0.75, 1.0},
    ["ORC_FEMALE"]        = {0.375, 0.5, 0.75, 1.0},

    ["BLOODELF_MALE"]    = {0.5, 0.625, 0.25, 0.5},
    ["BLOODELF_FEMALE"]    = {0.5, 0.625, 0.75, 1.0},

    ["DRAENEI_MALE"]    = {0.5, 0.625, 0, 0.25},
    ["DRAENEI_FEMALE"]    = {0.5, 0.625, 0.5, 0.75},

    ["GOBLIN_MALE"]        = {0.625, 0.750, 0.25, 0.5},
    ["GOBLIN_FEMALE"]    = {0.625, 0.750, 0.75, 1.0},

    ["WORGEN_MALE"]        = {0.625, 0.750, 0, 0.25},
    ["WORGEN_FEMALE"]    = {0.625, 0.750, 0.5, 0.75},
};
```

---

### CharacterCreate.xml

Now we are going to configure the position of the icons in their respective buttons and positions. To do this, we have to look for the race buttons section in `CharacterCreate.xml`. It goes something like this:

```xml
<CheckButton name="CharacterCreateRaceButton1" inherits="CharacterCreateRaceButtonTemplate" id="1">
    <Anchors>
        <Anchor point="TOP" relativePoint="TOP" x="-50" y="-61"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton2" inherits="CharacterCreateRaceButtonTemplate" id="2">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton1" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton3" inherits="CharacterCreateRaceButtonTemplate" id="3">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton2" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton4" inherits="CharacterCreateRaceButtonTemplate" id="4">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton3" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton5" inherits="CharacterCreateRaceButtonTemplate" id="5">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton4" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton6" inherits="CharacterCreateRaceButtonTemplate" id="6">
    <Anchors>
        <Anchor point="TOP" relativePoint="TOP" x="50" y="-61"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton7" inherits="CharacterCreateRaceButtonTemplate" id="7">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton6" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton8" inherits="CharacterCreateRaceButtonTemplate" id="8">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton7" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton9" inherits="CharacterCreateRaceButtonTemplate" id="9">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton8" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton10" inherits="CharacterCreateRaceButtonTemplate" id="10">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton9" relativePoint="BOTTOMLEFT" x="0" y="-21"/>
    </Anchors>
</CheckButton>
```

And we will replace it like this:

```xml
<CheckButton name="CharacterCreateRaceButton1" inherits="CharacterCreateRaceButtonTemplate" id="1">
    <Anchors>
        <Anchor point="TOP" relativePoint="TOP" x="-50" y="-50"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton2" inherits="CharacterCreateRaceButtonTemplate" id="2">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton1" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton3" inherits="CharacterCreateRaceButtonTemplate" id="3">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton2" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton4" inherits="CharacterCreateRaceButtonTemplate" id="4">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton3" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton5" inherits="CharacterCreateRaceButtonTemplate" id="5">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton4" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton8" inherits="CharacterCreateRaceButtonTemplate" id="8">
    <Anchors>
        <Anchor point="TOP" relativePoint="TOP" x="50" y="-50"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton9" inherits="CharacterCreateRaceButtonTemplate" id="9">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton8" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton10" inherits="CharacterCreateRaceButtonTemplate" id="10">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton9" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton11" inherits="CharacterCreateRaceButtonTemplate" id="11">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton10" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton12" inherits="CharacterCreateRaceButtonTemplate" id="12">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton11" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton7" inherits="CharacterCreateRaceButtonTemplate" id="7">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton12" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
<CheckButton name="CharacterCreateRaceButton6" inherits="CharacterCreateRaceButtonTemplate" id="6">
    <Anchors>
        <Anchor point="TOPLEFT" relativeTo="CharacterCreateRaceButton5" relativePoint="BOTTOMLEFT" x="0" y="-10"/>
    </Anchors>
</CheckButton>
```

---

### GlueParent.lua

Here we will put the track corresponding to each class as follows:

We search:

```lua
GlueAmbienceTracks["CHARACTERSELECT"] = "GlueScreenIntro";
```

And just below we add:

```lua
GlueAmbienceTracks["WORGEN"] = "GlueScreenHuman";
GlueAmbienceTracks["GOBLIN"] = "GlueScreenOrcTroll";
```

In the next "racelights" section, we must add our races. We are looking for this:

```lua
CHARACTERSELECT =  {
    {1,     0,  0.00000,        0.00000,        -1.00000,   1.0,    0.15000,    0.15000,    0.15000,    1.0,    0.00000,    0.00000,    0.00000},
    {1,     0,  -0.74919,       0.35208,        -0.56103,   1.0,    0.00000,    0.00000,    0.00000,    1.0,    0.44706,    0.54510,    0.73725},
    {1,     0,  0.53162,        -0.84340,       0.07780,    1.0,    0.00000,    0.00000,    0.00000,    2.0,    0.55,       0.338625,   0.148825},
```

And below we add this:

```lua
GOBLIN = {   
    {1,     0,  0.00000,        0.00000,        -1.00000,   1.0,    0.15000,    0.15000,    0.15000,    1.0,    0.00000,    0.00000,    0.00000},
    {1,     0,  -0.74919,       0.35208,        -0.56103,   1.0,    0.00000,    0.00000,    0.00000,    1.0,    0.44706,    0.54510,    0.73725},
    {1,     0,  0.53162,        -0.84340,       0.07780,    1.0,    0.00000,    0.00000,    0.00000,    2.0,    0.55,       0.338625,   0.148825},
},
WORGEN = {   
    {1,     0,  0.00000,        0.00000,        -1.00000,   1.0,    0.15000,    0.15000,    0.15000,    1.0,    0.00000,    0.00000,    0.00000},
    {1,     0,  -0.74919,       0.35208,        -0.56103,   1.0,    0.00000,    0.00000,    0.00000,    1.0,    0.44706,    0.54510,    0.73725},
    {1,     0,  0.53162,        -0.84340,       0.07780,    1.0,    0.00000,    0.00000,    0.00000,    2.0,    0.55,       0.338625,   0.148825},
```

Finally, we search:

```lua
-- Function to set the background model for character select and create screens
function SetBackgroundModel(model, name)
    local nameupper = strupper(name);
    local path = "Interface\\Glues\\Models\\UI_"..name.."\\UI_"..name..".m2";
    if ( model == CharacterCreate ) then
        SetCharCustomizeBackground(path);
    else
        SetCharSelectBackground(path);
    end
    PlayGlueAmbience(GlueAmbienceTracks[nameupper], 4.0);
    SetLighting(model, nameupper)
end
```

And we replace it with this, which will give us the creation wallpaper for our custom races:

```lua
-- Function to set the background model for character select and create screens
function SetBackgroundModel(model, name)
    local nameupper = strupper(name);
        if (name == "Goblin" or name == "GOBLIN") then
            name = "Orc";
        end
        if (name == "Worgen" or name == "WORGEN") then
            name = "HUMAN";
        end
    local path = "Interface\\Glues\\Models\\UI_"..name.."\\UI_"..name..".m2";
    if ( model == CharacterCreate ) then
        SetCharCustomizeBackground(path);
    else
        SetCharSelectBackground(path);
    end
    PlayGlueAmbience(GlueAmbienceTracks[nameupper], 4.0);
    SetLighting(model, nameupper)
end
```

---

### GlueStrings.lua

This file is to define the information related to the characters. We are going to change several things: race information and skill information.

We are looking for this:

```lua
ABILITY_INFO_BLOODELF1 = "- Increased enchanting skill.";
```

And we add above that line this:

```lua
ABILITY_INFO_GOBLIN1 = "- Goblin skills, line 1";
ABILITY_INFO_GOBLIN2 = "- Goblin skills, line 2";
ABILITY_INFO_GOBLIN3 = "- Goblin skills, line 3";
ABILITY_INFO_GOBLIN4 = "- Goblin skills, line 4";
ABILITY_INFO_WORGEN1 = "- Worgen skills, line 1";
ABILITY_INFO_WORGEN2 = "- Worgen skills, line 2";
ABILITY_INFO_WORGEN3 = "- Worgen skills, line 3";
ABILITY_INFO_WORGEN4 = "- Worgen skills, line 4";
```

Now we look for this:

```lua
RACE_CHANGE_IN_PROGRESS = "Updating race...";
```

And then we add:

```lua
RACE_INFO_GOBLIN = "Information about Goblins.";
RACE_INFO_GOBLIN_FEMALE = "Information about female Goblins.";
RACE_INFO_WORGEN = "Information about Worgens.";
RACE_INFO_WORGEN_FEMALE = "Information about female Worgens.";
```

And we’re finally done!!

We would only have to download the following file: [DOWNLOAD](https://mega.nz/file/eQAwRLCJ#jYy5eVWR_ZFG0ftWtF25u6_MEzqIfm-hT1EvPAi51yk). This is saved in the "Interface/Glues/CharacterCreate" path within the patch. The same thing, the icons do not match the coordinates that we have put in the tutorial since I did this file long after doing this tutorial and it has many more icons of more races. For them to match, the Cataclysm client's BLP would have to be used.

The `.lua` files go inside the patch in the "Interface/GlueXML" path.
