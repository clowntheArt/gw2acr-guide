---
title: "GW2 ACR Guide"
toc: true
---

# gw2acr - how to build an acr

this guide is my accumulation of info from ctrl + f'ing the discord and my understanding of what an acr is

## what an acr is (and is not)

an **acr** defines what actions are executed and under which conditions for **one exact skill bar**

* evaluated continuously during combat
* executes the **highest-priority valid chain**
* reacts to cooldowns, buffs and character state

an acr is **not a script**. it does not replay a fixed sequence

## core building blocks

### actions

a single executable step, such as:

* casting a skill
* weapon swapping
* activating a profession mechanic

actions may define targeting, parameters and continuation behavior

### chains

a **chain** is an ordered list of actions

rules:

* actions execute top to bottom
* a chain can only start if all its conditions are met
* once started, the chain runs to completion

chains are the basic units of rotation logic

keep chains short and purpose-driven. long chains are harder to reason about and easier to break

### priority

chains are evaluated from top to bottom

* the first valid chain is executed
* priority determines execution order

priority represents value, not sequence
if two chains can run, the higher one always wins

most rotation problems are priority problems, not condition problems

## chain types

gw2acr supports four distinct chain types. they differ in when and how they are evaluated

* **opener chains**
* **main chains**
* **side chains**
* **stunbreak chains**

understanding the differences is critical to building correct logic

## creating a new acr

### 1. prepare the character state

before creating an acr, the character must exactly match the intended setup:

* weapons
* traits and specializations
* utility skills
* transform state (shroud, kits, etc.)

any mismatch prevents the acr from activating
if an acr does not load, recreate it from the live character state

### 2. capture the skill bar

* open the **acr editor**
* select **new acr from current skill bar**
* name the acr clearly (e.g. `reaper_gs`, `tempest_fire`)

save immediately

## structuring an acr

### common structure

1. **opener chains** one-time setup
2. **main chains** core repeatable logic
3. **side chains** instant parallel actions
4. **filler chains** low-priority sequential actions
5. **stunbreak chains** control recovery

not every acr requires all layers, but this structure scales well

## opener chains

* execute once per combat
* disabled after the main rotation begins
* used for setup, long cooldowns or state entry

openers should be short and intentional
if an opener fires more than once per fight, it is misclassified

## main chains

main chains define repeatable combat behavior

guidelines:

* keep chains short
* use conditions instead of waits
* let priority determine usage frequency

avoid encoding full rotations as a single chain
prefer multiple small chains with clear intent

## side chains

side chains run **in parallel** with normal chains

hard rules:

* normal chains run in sequence
* side chains do not block or interrupt normal chains
* **side chains can only contain instant cast actions**
* **non-instant skills cannot be placed in side chains**

side chains are evaluated continuously and may fire while another chain is already executing, as long as their conditions are met

if a skill has a cast time, channel, or animation lock, it does not belong in a side chain

### what side chains are for

side chains are used exclusively for:

* instant cast utility skills
* buffs that should be used on cooldown
* reactive effects that must not delay the rotation
* opportunistic skills that should fire whenever possible

side chains exist to add behavior without affecting sequencing

### side chains vs filler chains

side chains and filler chains serve different purposes

* filler chains run when nothing else is available
* side chains run regardless of what else is happening

filler chains still respect sequential execution
side chains bypass it entirely

use filler chains for low-priority rotation skills
use side chains only for instant, non-blocking actions

## filler chains

filler chains run when no higher-priority main chain is valid

typical uses:

* auto-attacks
* low-impact skills
* low-priority rotation abilities

best practice:

* few or no conditions
* lowest priority

filler chains absorb timing variance and prevent idle time

## stunbreak chains

stunbreak chains are evaluated separately from other chains

* they trigger only under control-impairing conditions
* they should contain only stunbreak or survival actions
* they should be kept minimal and defensive

do not place general utility or damage skills in stunbreak chains

## conditions

conditions control when a chain or action may execute

common examples:

* skill cooldown availability
* buff or debuff presence
* resource thresholds
* combat state

conditions can be inverted to express exclusions

### inverting conditions

green condition names can be inverted

inversion is often cleaner than duplicating logic
prefer one inverted condition over two nearly identical chains

## continue triggers and timing

continue triggers determine when the next action may begin

guidelines:

* trigger `0` is always safe
* later triggers allow tighter chaining
* channeling or looping skills require care

use triggers to enforce animation safety instead of fixed delays
fixed waits tend to break under alacrity or latency

## targeting logic

each action can define its own targeting behavior:

* current target
* self
* ground-targeted logic
* profession-specific behavior

do not assume default targeting is correct for all skills
mis-targeted skills are a common silent failure

## interrupts and instants

* interrupts are handled automatically
* instant skills belong in side chains or low-priority filler
* avoid blocking long chains with instants unless intended

instants should be opportunistic, not rotational anchors

## testing and debugging

use the **visual debugger** to:

* observe chain evaluation
* identify failed conditions
* verify priority order

if something does not fire, check priority before adding more conditions

## common mistakes

* treating acrs as scripts
* using overly long chains
* relying on fixed wait timers
* incorrect priority ordering
* skill-bar mismatches

most issues come from over-specifying behavior instead of letting priority work

## design principle

an acr should always select the highest-value action that is safe to execute

use priority and conditions to encode intent, not button order
