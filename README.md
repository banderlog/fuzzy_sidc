# Fuzzy SIDC

## Description

Use [rapidfuzz](https://github.com/rapidfuzz/RapidFuzz) to generate NATO Symbol identification coding (SIDC) using prepared MIL-STD-2525D or STANAG-APP6D data.

I am using Måns Beckman repos (links below) as data source and as SIDC to SVG converter (embedding JS via [PyMiniRacer](https://github.com/sqreen/PyMiniRacer)).

![](pics/sets.png)

## Links

- Click selection of SIDC: <https://spatialillusions.com/unitgenerator/>
- SIDC to description: <https://sidc.milsymb.net/>
- STANAG-APP6 data: <https://github.com/spatialillusions/stanag-app6>
- MIL-STD-2525 data: <https://github.com/spatialillusions/mil-std-2525>
- SIDC to SVG: <https://github.com/spatialillusions/milsymbol>
- SVG to PNG: <https://cairosvg.org/>
- [Official MIL-STD-2525D manual](https://www.jcs.mil/portals/36/documents/doctrine/other_pubs/ms_2525d.pdf)

## Installation

`pip install fuzzy_sidc`

## Examples

### As a standalone command-line program

```bash
```

### As Python class

#### Get SIDC from description

```python
from fuzzy_sidc import SIDCFuzzySearcher

# you need to download milsynbol.js
path_to_set_a = 'set_a.json'
path_to_set_b = 'set_b_2525d.json'  # or 'set_b_app6d.json'
path_to_milsymboljs = 'milsymbol.js'
x = SIDCFuzzySearcher(path_to_set_a, path_to_set_b, path_to_milsymboljs)

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
