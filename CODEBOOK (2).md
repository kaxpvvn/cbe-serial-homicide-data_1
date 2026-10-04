# Codebook

What each variable and code in the dataset means. Each row is **one serial homicide offender** (n = 150).

> **Note:** column names are as in the CSV header (note `Gender` with a capital G).

## How to read a cell

- Behavioral variables use short codes: a letter prefix for the variable plus a number (e.g., `V0`, `A1`, `M2`).
- **Multi-label** variables can hold several codes, separated by commas. For example, `approach = "A0, A1"` means the offender both abducted victims (`A0`) and lured them (`A1`).
- **Single-valued** variables hold exactly one code.

## Variables at a glance

| Variable | Meaning | Codes | Type | Used in CBE |
|----------|---------|-------|------|:-----------:|
| `case` | Case label (the offender's name as given in the public sources) | – | – | – |
| `Gender` | Offender gender (`Male` / `Female`) | – | single | – |
| `country` | Country of the offenses, as a text label: `USA` (132), `UK` (7), `Canada` (3), `Australia` (3), `England` (2), `Soviet Union` (1), `Hungary` (1), `Mexico` (1). `England` and `UK` are separate labels. | – | single | – |
| `year` | Active-offending period, stored as text: a range (`1974-1978`), a single year (`1969`) or a decade (`1960s`) | – | – | – |
| `victim` | Victim sex and age | V0–V4 | multi | ✔ |
| `target_type` | How victims were chosen | T0–T1 | single | – |
| `location` | Where the offenses occurred | L0–L2 | multi | ✔ |
| `approach` | How the victim was approached | A0–A2 | multi | ✔ |
| `method` | Method of killing | M0–M2 | multi | ✔ |
| `disposal` | What was done with the body | D0–D2 | multi | ✔ |
| `signature` | Signature behavior | S0–S1 | single | – |
| `organization` | Organized vs. disorganized | O0–O1 | multi (1 record dual-coded) | ✔ |
| `MO_consistency` | Consistency of modus operandi | C0–C1 | single | – |
| `relationship` | Offender–victim relationship | R0–R2 | multi | ✔ |
| `motive_type` | Motive | MT0–MT3 | multi | ✔ |
| `diagnosed_Mental_Condition` | Mental condition per legal determination | DMC0–DMC2 | single | – |
| `Error` | Investigative error type | E0–E5 | single | – |
| `Caught` | Capture status | CS0–CS1 | single | – |

## Behavioral variables

### `victim` — victim sex and age (multi-label)

| Code | Meaning |
|------|---------|
| V0 | Male (aged 20–59) |
| V1 | Female (aged 20–59) |
| V2 | Boy (aged 0–19) |
| V3 | Girl (aged 0–19) |
| V4 | Elderly (aged 60 and over) |

### `target_type` — how victims were chosen (single)

| Code | Meaning |
|------|---------|
| T0 | Selected from a specific group (e.g., women with long hair) |
| T1 | Selected randomly, non-specific |

### `location` — where the offenses occurred (multi-label)

| Code | Meaning |
|------|---------|
| L0 | Enclosed space (house, hotel, room) |
| L1 | Public space (park, street) |
| L2 | Desolate area (isolated place, forest) |

### `approach` — how the victim was approached (multi-label)

| Code | Meaning |
|------|---------|
| A0 | Abduction |
| A1 | Luring / deception |
| A2 | Threats / coercion |

### `method` — method of killing (multi-label)

| Code | Meaning |
|------|---------|
| M0 | Torture (strangulation, asphyxiation) |
| M1 | Use of a weapon |
| M2 | Poison / toxic substances |

### `disposal` — disposal of the body (multi-label)

| Code | Meaning |
|------|---------|
| D0 | No handling of the body (left where it was) |
| D1 | Concealed (buried, hidden, destroying evidence) |
| D2 | Dumped (killed, then taken and left elsewhere) |

### `signature` — signature behavior (single)

| Code | Meaning |
|------|---------|
| S0 | Absent |
| S1 | Present |

### `organization` — organization of the offense (multi-label; one record dual-coded `O0, O1`)

| Code | Meaning |
|------|---------|
| O0 | Organized (planned) |
| O1 | Disorganized (random, unplanned) |

### `MO_consistency` — consistency of modus operandi (single)

| Code | Meaning |
|------|---------|
| C0 | Changed method |
| C1 | Used the same method |

### `relationship` — offender–victim relationship (multi-label)

| Code | Meaning |
|------|---------|
| R0 | Strangers (did not know the victim) |
| R1 | Acquaintances (knew the victim) |
| R2 | Both |

### `motive_type` — motive (multi-label)

| Code | Meaning |
|------|---------|
| MT0 | Sexual motive |
| MT1 | Material gain / property |
| MT2 | Violence / power / recognition |
| MT3 | Belief / religion |

### `diagnosed_Mental_Condition` — based on legal determination (single)

| Code | Meaning |
|------|---------|
| DMC0 | No information found, or insufficient evidence |
| DMC1 | Mentally ill |
| DMC2 | Not mentally ill |

## Outcome variables

### `Error` — investigative error type (single)

Unordered categories (not an ordinal scale).

| Code | Meaning |
|------|---------|
| E0 | No clear error |
| E1 | Left evidence behind (DNA / fingerprints / witnesses) |
| E2 | Poor body disposal (failed concealment / easily found) |
| E3 | Changed behavior, breaking own pattern |
| E4 | Repeatedly contacted the victim/acquaintances, leading to capture |
| E5 | Caught through an external factor (e.g., coincidence / being seen) |

### `Caught` — capture status (single)

| Code | Meaning |
|------|---------|
| CS0 | Not caught (n = 4) |
| CS1 | Caught (n = 146) |

## Composite Behavioral Entropy (CBE)

CBE is a derived score, not a column in the data. It is computed from the eight multi-label dimensions marked ✔ above. For offender *i* and dimension *j*, let *k* be the number of distinct codes listed and K<sub>j</sub> the number of codes in that dimension (victim 5, motive_type 4, location/approach/method/disposal/relationship 3, organization 2):

```
H~(i,j) = log2(k) / log2(Kj)           # 0 if one code, 1 if all codes present
CBE_i   = mean of H~(i,j) over the 8 dimensions
```

Because only presence/absence is recorded (not frequency), CBE is a normalized **category-richness** index rather than a frequency-based Shannon entropy. `CBE_core5` uses only `location`, `approach`, `method`, `disposal` and `victim`.

## Data-quality notes

- No missing values on any variable.
- Trailing whitespace (three `Gender` values, one `year` value, two `case` labels) was trimmed, the header `target _type` was renamed to `target_type`, and one `approach` cell written `A1,A2` was reformatted to `A1, A2`. These formatting edits did not change any code.
- One record was originally coded `DMT2` on `diagnosed_Mental_Condition` (outside the DMC0–DMC2 scheme). The authors recoded it to **DMC2 by their own judgment, not verified against the primary source**, and the CSV contains the recoded value. This variable is not used in CBE; re-check the row before relying on it.
- One record is dual-coded `O0, O1` on `organization`.
- `Caught` is extremely imbalanced (146 CS1 vs. 4 CS0), a structural property of publicly documented rosters.
