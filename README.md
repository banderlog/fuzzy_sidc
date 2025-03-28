# Fuzzy SIDC

[![PyPI Downloads](https://static.pepy.tech/badge/fuzzy-sidc)](https://pepy.tech/projects/fuzzy-sidc) [![PyPI Downloads](https://static.pepy.tech/badge/fuzzy-sidc/month)](https://pepy.tech/projects/fuzzy-sidc) [![PyPI Downloads](https://static.pepy.tech/badge/fuzzy-sidc/week)](https://pepy.tech/projects/fuzzy-sidc)

## Description

I am using Måns Beckman repos (links below) as data source and as SIDC to SVG converter (embedding JS via [PyMiniRacer](https://github.com/sqreen/PyMiniRacer)).

**Functionality:**
1. Use [rapidfuzz](https://github.com/rapidfuzz/RapidFuzz) to generate NATO Symbol identification coding (SIDC) using prepared MIL-STD-2525D or STANAG-APP6D data.
2. Use milsymbol.js to get SVG icon for provided SIDC
3. Can be used as CLI or as Python class object
4. Any other symbol coding standard could be used, if data converted to proper format (see jupyter notebooks) and milsymbol.js supports it (if you need SVG)

For better results, you need to query set A, set B and modifiers separately (refer to image below).  
Modifiers choices are linked to set B result.  
For set A each word in query treats separately.  
For set B and modifiers we look for the whole query as is.  
You can search the whole data for specific query and just get TOP n matches, to help you to figure out what you want (refer to "Resolve difficult situations" section).

![](pics/sets.png)


## Links

- Click selection of SIDC: <https://spatialillusions.com/unitgenerator/>
- SIDC to description: <https://sidc.milsymb.net/>
- STANAG-APP6 data: <https://github.com/spatialillusions/stanag-app6>
- MIL-STD-2525 data: <https://github.com/spatialillusions/mil-std-2525>
- SIDC to SVG: <https://github.com/spatialillusions/milsymbol>
- SVG to PNG: <https://cairosvg.org/>
- [Official MIL-STD-2525D manual](https://www.jcs.mil/portals/36/documents/doctrine/other_pubs/ms_2525d.pdf)


## Installation and Building

### Install

```bash
pip install fuzzy_sidc
```

### Build

```bash
git clone https://github.com/banderlog/fuzzy_sidc
cd fuzzy_sidc
python3 -m venv venv
./venv/bin/pip install -r requirements.txt
./venv/bin/pip install build
./venv/bin/python -m build -s
```


## Examples

### As command-line program

#### Usual stuff

Get SIDC:

```bash
# -a -- query for Set A
# -b -- query for set B
# -m1 and -m2 -- queries for modifier1 and modifier2
./venv/bin/python -m fuzzy_sidc -a "Hostile Realty Land Present Platoon TaskForce" -b "mortar armore" -m1 sniper -m2 airborn

10061004141308016101
```

Get SVG:

```bash
./venv/bin/python -m fuzzy_sidc --sidc2svg 10061004141308016101

# or

./venv/bin/python -m fuzzy_sidc -a "Hostile Realty Land Present Platoon TaskForce" -b "mortar armore" -m1 sniper -m2 airborn --svg
```
```
<svg xmlns="http://www.w3.org/2000/svg" version="1.2" baseProfile="tiny" width="53.2" height="67.2" viewBox="24 -16 152 192"><path d="M 100,28 L172,100 100,172 28,100 100,28 Z" stroke-width="4" stroke="black" fill="rgb(255,128,128)" fill-opacity="1" ></path><circle cx="100" cy="115" r="5" stroke-width="3" stroke="black" fill="none" ></circle><path d="M100,111 l0,-30 M90,90 l10,-10 10,10" stroke-width="3" stroke="black" fill="none" ></path><path d="m 90,125 h 20 c 10,0 10,15 0,15 H 90 c -10,0 -10,-15 0,-15" stroke-width="3" stroke="black" fill="none" ></path><path d="m 120,65 -11,0 m 11,10 -14,0 m 4,-14 -30,0 0,18 25,0 z m 10,2 0,14" stroke-width="3" stroke="black" fill="none" ></path><path d="M55,28 L55,-12 145,-12 145,28" stroke-width="4" stroke="black" fill="none" ></path><g transform="translate(0,0)" stroke-width="4" stroke="black" fill="none" ><circle cx="100" cy="8" r="7.5" fill="black" ></circle><circle cx="70" cy="8" r="7.5" fill="black" ></circle><circle cx="130" cy="8" r="7.5" fill="black" ></circle></g></svg>
```


#### Search and show what it found

```bash
# -- show
./venv/bin/python -m fuzzy_sidc -a "Hostile Realty Land Present Platoon TaskForce" -b "mortar armore" -m1 sniper -m2 airborn --show

Hostile:
        ('4.Hostile/Faker', 100.0, 11)
Realty:
        ('3.Reality', 83.33333333333334, 2)
Land:
        ('56.Land Unit', 100.0, 17)
Present:
        ('7.Present', 100.0, 35)
Platoon:
        ('910.Platoon/detachment', 100.0, 53)
TaskForce:
        ('8.Task Force', 88.88888888888889, 45)
mortar armore:
        ('Land unit.Fires.Mortar.Armored/Mechanized/Tracked', 76.92307692307692, 1566)
sniper:
        ('Land unit.modifier_1.Sniper', 90.9090909090909, 59)
airborn:
        ('Land unit.modifier_2.Airborne', 85.71428571428572, 1)
10061004141308016101
```


#### Resolve difficult situations

Try to get usual Tank SIDC:

```bash
./venv/bin/python -m fuzzy_sidc --std app6d -b Tank --show
Tank:
        ('Control Measures.Airspace Control Points.Tanking', 100.0, 157)
10010000001812000000
```

Oh, it is not what we are looking for, lets see TOP 10 results for 'Tank':

```bash
# -s -- search the whole data
# -n 10 -- show TOP 10 results for each set
#          modifiers are not included, because different set B entities have different modifiers
# --threshold  74 -- drop all findings with similarity score (normalized edit distance) >=75
./venv/bin/python -m fuzzy_sidc --std app6d -s Tank -n 10 --threshold 75
IN SET A -- Tank:
        ('8.Task Force', 75.0, 45)
        ('8.Feint/Dummy Task Force', 75.0, 46)
        ('8.Task Force Headquarters', 75.0, 47)
        ('8.Feint/Dummy Task Force Headquarters', 75.0, 48)

IN SET B -- Tank:
        ('Control Measures.Airspace Control Points.Tanking', 100.0, 157)
        ('Sea surface.Civilian.Merchant Ship, General.Merchant Ship, Tanker', 100.0, 689)
        ('Sea surface.modifier_2.Tank', 100.0, 718)
        ('Land equipment.Vehicles.Tank.', 100.0, 870)
        ('Land equipment.Vehicles.Tank.Light', 100.0, 871)
        ('Land equipment.Vehicles.Tank.Medium', 100.0, 872)
        ('Land equipment.Vehicles.Tank.Heavy', 100.0, 873)
        ('Land equipment.Vehicles.Tank Recovery Vehicle.', 100.0, 874)
        ('Land equipment.Vehicles.Tank Recovery Vehicle.Light', 100.0, 875)
        ('Land equipment.Vehicles.Tank Recovery Vehicle.Medium', 100.0, 876)
```

Aha, so we need 'Vehicle.Tank'.
Also, let's change defaults in set A by passing JSON string:

```bash
#  -- defaults_set_a -- chnage default values for set A, idx:value
./venv/bin/python -m fuzzy_sidc --std app6d -b Vehicle.Tank.Heavy --defaults_set_a '{"4": "6", "56": "10"}' --show

Vehicle.Tank.Heavy:
        ('Land equipment.Vehicles.Tank.Heavy', 94.44444444444444, 873)
10090000001202030000
```

Same result with query for set A:

```bash
./venv/bin/python -m fuzzy_sidc --std app6d -b Vehicle.Tank.Heavy -a "Land Hpstile" --show
Land:
        ('56.Land Unit', 100.0, 17)
Hpstile:
        ('4.Hostile/Faker', 85.71428571428572, 11)
Vehicle.Tank.Heavy:
        ('Land equipment.Vehicles.Tank.Heavy', 94.44444444444444, 873)
10061000001202030000
```


### As Python class

#### Load/create class object

To use supplied by package data:

```python
from fuzzy_sidc import get_preloaded_SIDCFuzzySearcher

x = get_preloaded_SIDCFuzzySearcher()  # 2525d
x = get_preloaded_SIDCFuzzySearcher('app6d')
```
To use with external json/js:

```python
from fuzzy_sidc import SIDCFuzzySearcher

# assuming you downloaded all those files and they are in same dir
path_to_set_a = 'set_a.json'
path_to_set_b = 'set_b_2525d.json'  # or 'set_b_app6d.json'
path_to_milsymboljs = 'milsymbol.js'
x = SIDCFuzzySearcher(path_to_set_a, path_to_set_b, path_to_milsymboljs)
```


#### Get SIDC from description

```bash
# search in set A
query_a = "Hostile Realty Land Present Platoon TaskForce"
# search in set B
query_b = "mortar armore"
# mod1 and mod2 are modifiers
x.get_sidc(query_a=query_a, query_b=query_b, mod1='sniper', mod2='airborn')
```
```txt
'10061004141308016101'
```


#### Get SVG from SIDC

```python
x.get_svg('10061004141308016101')
```
```txt
'<svg xmlns="http://www.w3.org/2000/svg" version="1.2" baseProfile="tiny" width="53.2" height="67.2" viewBox="24 -16 152 192"><path d="M 100,28 L172,100 100,172 28,100 100,28 Z" stroke-width="4" stroke="black" fill="rgb(255,128,128)" fill-opacity="1" ></path><circle cx="100" cy="115" r="5" stroke-width="4" stroke="black" fill="none" ></circle><path d="M100,111 l0,-30 M90,90 l10,-10 10,10" stroke-width="4" stroke="black" fill="none" ></path><path d="M 70,120 l 60,0 c10,0 10,10 0,10 l -60,0 c-10,0 -10,-10 0,-10" stroke-width="4" stroke="black" fill="none" ></path><path d="m 120,65 -11,0 m 11,10 -14,0 m 4,-14 -30,0 0,18 25,0 z m 10,2 0,14" stroke-width="4" stroke="black" fill="none" ></path><path d="M55,28 L55,-12 145,-12 145,28" stroke-width="4" stroke="black" fill="none" ></path><g transform="translate(0,0)" stroke-width="4" stroke="black" fill="none" ><circle cx="100" cy="8" r="7.5" fill="black" ></circle><circle cx="70" cy="8" r="7.5" fill="black" ></circle><circle cx="130" cy="8" r="7.5" fill="black" ></circle></g></svg>'
```


#### Get SIDC from description, but show its meaning

```python
x.get_sidc(query_a=query_a, query_b=query_b, mod1='sniper', mod2='airborn', show_results=True)
```
```txt
Hostile:
	('4.Hostile/Faker', 100.0, 11)
Realty:
	('3.Reality', 83.33333333333334, 2)
Land:
	('56.Land Unit', 100.0, 17)
Present:
	('7.Present', 100.0, 35)
Platoon:
	('910.Platoon/detachment', 100.0, 53)
TaskForce:
	('8.Task Force', 88.88888888888889, 45)
mortar armore:
	('Land unit.Fires.Mortar.Armored/Mechanized/Tracked', 76.92307692307692, 1566)
sniper:
	('Land unit.modifier_1.Sniper', 90.9090909090909, 59)
airborn:
	('Land unit.modifier_2.Airborne', 85.71428571428572, 1)
'10061004141308016101'
```


#### Just search data fot TOP n matches

```python
# change threshold
x.score_cutoff = 77
# show TOP 10
x.show_top_n('airborne'. n=10)
```
```txt
IN SET A -- airborne:
	NOTHING

IN SET B -- airborne:
	('Land unit.modifier_2.Airborne', 93.33333333333333, 1791)
	('Control Measures.Maneuver Areas.Axis of Advance.Friendly Airborne/Aviation', 87.5, 79)
	('Control Measures.Airspace Control Points.Airborne Early Warning (AEW) Station', 87.5, 130)
	('Signals intelligence.modifier_1.Airborne Search and Bombing', 87.5, 1363)
	('Signals intelligence.modifier_1.Airborne Intercept', 87.5, 1364)
	('Signals intelligence.modifier_1.Airborne Reconnaissance and Mapping', 87.5, 1366)
	('Air.Military.Fixed Wing.Airborne Command Post (ACP)', 87.5, 2168)
	('Air.Military.Fixed Wing.Airborne Early Warning (AEW)', 87.5, 2169)
	('Air.modifier_1.Airborne Command Post (ACP)', 87.5, 2215)
	('Air.modifier_1.Airborne Early Warning (AEW)', 87.5, 2216)
```


#### Change default set A values

```python
# refer to manual or set_a.json for meaning
# set standard identity to 'Hostile/Faker' and symbol set to 'Land Unit'
x.defaults_set_a.update({'4': '6', '56': '10'})
```
