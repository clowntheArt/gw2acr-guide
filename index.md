# gw2acr — How to Build an ACR

A practical, decision-focused guide to designing **Automated Combat Routines (ACRs)** in gw2acr. This page explains *how to think* about rotations, not just where to click.

---

## What an ACR Is (and Is Not)

An **ACR** defines *what your character should do* and *under which conditions* for **one exact skill bar**.

* It is evaluated continuously during combat
* It executes the **highest‑priority valid chain** at any moment
* It adapts dynamically to cooldowns, buffs, and state

**An ACR is not a script.** It does not replay a fixed sequence. It encodes decision logic.

---

## Core Building Blocks

### Actions

A single executable step:

* Casting a skill
* Weapon swap
* Profession mechanic

Actions may define targeting, parameters, and continuation behavior.

---

### Chains

A **chain** is an ordered list of actions.

Rules:

* Actions execute top → bottom
* A chain can only start if all its **conditions** are true
* Once started, the chain runs to completion

Chains are the atomic units of rotation logic.

---

### Priority

Chains are evaluated **from top to bottom**.

* The first valid chain is executed
* Priority replaces rigid sequencing

> **Key idea:** Priority expresses *value*, not order.

---

## Creating a New ACR

### 1. Prepare the Character State

Before creating an ACR, your character must match the intended setup exactly:

* Weapons
* Traits and specializations
* Utility skills
* Transform state (shroud, kits, etc.)

Any mismatch will prevent the ACR from activating.

---

### 2. Capture the Skill Bar

* Open the **ACR Editor**
* Select **New ACR from current skill bar**
* Name the ACR clearly (e.g. `Reaper_GS`, `Tempest_Fire`)

Save immediately.

---

## Structuring a Good ACR

Well‑designed ACRs are layered, not linear.

### Common Structure

1. **Opener chains** – one‑time setup
2. **Burst / cooldown chains** – high‑value skills
3. **Main loop chains** – repeatable core logic
4. **Filler chains** – low‑priority actions
5. **Safety / constraint chains** – prevent mistakes

Not all ACRs need every layer, but this structure scales cleanly.

---

## Opener Chains

* Execute once per combat
* Lock out after the main rotation begins
* Used for setup, long cooldowns, or state entry

Openers should be short and intentional.

---

## Main Rotation Chains

Main chains represent the **repeatable decision logic** of the build.

Design rules:

* Keep chains short
* Gate actions with conditions, not waits
* Let priority determine frequency

Avoid encoding entire rotations as a single chain.

---

## Filler Chains

Filler chains execute when nothing more valuable is available.

Typical uses:

* Auto‑attacks
* Low‑impact skills
* Instant casts

Best practice:

* Minimal or no conditions
* Lowest priority

---

## Conditions (Where Logic Lives)

Conditions define *when* a chain or action is allowed to execute.

Common examples:

* Skill cooldown ready
* Buff present or missing
* Resource thresholds
* Combat state

Conditions can be inverted to express exclusions cleanly.

---

## Continue Triggers & Timing

Continue triggers control **when the next action may begin**.

Guidelines:

* Trigger `0` is always safe
* Later triggers allow tighter chaining
* Channeling or looping skills require care

Use triggers to enforce animation safety instead of fixed waits.

---

## Targeting Logic

Each action can define its own targeting mode:

* Current target
* Self
* Ground‑targeted logic
* Profession‑specific behavior

Never assume default targeting is correct for all skills.

---

## Interrupts & Instants

* Interrupts are handled automatically
* Instant skills can be placed in filler or side chains
* Avoid blocking long chains with instants unless intentional

---

## Testing & Debugging

Use the **visual debugger** to:

* See which chains are evaluated
* Understand why conditions fail
* Verify priority ordering

Test incrementally and adjust one variable at a time.

---

## Common Mistakes

* Treating ACRs like scripts
* Overusing long fixed chains
* Relying on fixed wait timers
* Misordered priorities
* Forgetting exact skill‑bar matching

---

## Design Philosophy

A strong ACR answers one question repeatedly:

> *What is the most valuable **safe** action I can perform right now?*

Encode intent, not button order. Let priority and conditions do the work.
