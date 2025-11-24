# Pkmn Event Archiver/Pipeline-Converter

This project **automates the processing of event files** (such as `.pcd`, `.pgf`, `.wc4`, `.wc5`, and similar) for the **Nintendo DS/Wii generation Pokémon games (Gen IV & Gen V)**.

The pipeline uses a central **CSV database (`events.csv`)** to automatically assign events to the correct **GameCodes** and **Regions** and generate the ready-to-use game files.

## Table of Contents

- [Structure](#structure)
    - [File Components](#file-components)
    - [Structure of `events.csv`](#detail-structure-of-eventscsv)
- [How It Works](#how-it-works)
    - [CSV Check (Primary)](#1-csv-check-primary)
    - [Fallback (YAML) – Secondary](#2-fallback-yaml--secondary)
- [Usage](#usage)
    - [Command Execution](#command-execution)
    - [I/O Types](#io-types)
    - [Assignment Examples](#assignment-examples)
- [Data Sources](#data-sources)
- [Contributing](#contributing)
- [License](#license)

---

## Structure

### File Components

| File | Purpose | Details |
| :--- | :--- | :--- |
| `events.csv` | **Central Event Database** | Contains all known Gen IV & Gen V Pokémon events. |
| `mapping.yml` | **Fallback Rules** | Stores **Regex patterns** for events not found in the CSV (to ensure robustness). |
| `mg_pipeline.py` | **Main Script** | Processes input files and generates the final `.myg` outputs. |

### Detail: Structure of `events.csv`

The CSV database defines the mapping using the following key columns:

* **`EventName`**: The clear name of the event (e.g., `Movie Darkrai 2007`).
* **`GameCodes`**: The target games for which the event is valid (e.g., `ADAE, CPUE`).
* **`Regions`**: The regions where the event was distributed (e.g., `JPN, USA, EUR`).
* **`Year`**: The year of distribution.

---

## How It Works

The processing follows a **two-stage process** to ensure maximum reliability in mapping event files.

### 1. CSV Check (Primary)

1.  The pipeline loads `events.csv`.
2.  **Tokens** are extracted from the input file's name.
3.  If an **`EventName`** is found in the CSV, it is assigned to all listed **`GameCodes`**.

#### Multi-Target Events

**Example:**

```csv
Movie Darkrai 2007,"ADAE,CPUE","JPN,USA,EUR",2007
```
Result: A .myg file is generated in both DLC/ADAE/ and DLC/CPUE/.

### 2. Fallback (YAML) – Secondary

If no CSV match is found, Regex rules from mapping.yml are applied to enforce a GameCode assignment.
This ensures robustness for new or unknown events.

---

## Usage

The main script is executed via the command line and requires the paths to the configuration files and the converter binary.

### Command Execution
```bash
python mg_pipeline.py \
    --events-csv config/events.csv \
    --mapping config/mapping.yml \
    --bin-mgc bin/mgc_converter
```

### I/O Types

| I/O Type | Description |
| :--- | :--- |
| **Input** | Event files (`.pcd`, `.pgf`, `.wc4`, `.wc5`) |
| **Output** | `.myg` files, stored in the respective `DLC/<GameCode>/` folders |

### Assignment Examples

| Input Filename | CSV Match (Example) | Output Path |
| :--- | :--- | :--- |
| `059 GameStop Raikou ENG [PPorg].pcd` | `GameStop Raikou, "IPGE", "USA, EUR", 2011` | `DLC/IPGE/059 GameStop Raikou ENG [PPorg].myg` |
| `028 McDonald's Pikachu JPN [PPorg].pcd` | `McDonald's Pikachu, "IPGE", "JPN", 2010` | `DLC/IPGE/028 McDonald's Pikachu JPN [PPorg].myg` |

---

## Data Sources

* [Project Pokémon – Gen IV Event Collection](https://projectpokemon.org)
* [Bulbapedia – Event Pokémon Distributions](https://bulbapedia.bulbagarden.net)
* [PokéWiki – Events 4th & 5th Generation](https://pokewiki.de)

---

## Contributing

* Always add new events first to **`events.csv`**.
* Use **consistent naming conventions**: `Movie <Pokémon> <Year>` or `Pokémon Center <Pokémon>`.
* Each entry should have only **one year** (e.g., `2010`) and **no year ranges** (e.g., `2010-2013`).
* Pull Requests are welcome!

---

## License

Community project for archiving and processing Pokémon events. All event files come from publicly documented sources (Bulbapedia, Project Pokémon, PokéWiki).