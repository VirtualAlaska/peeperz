# Peeperz

Peeperz is a digital collectible project featuring 6,969 unique items, each with its own set of traits and rarity. This repository contains all assets, metadata, scripts, and resources related to the Peeperz collection.

## Repository Structure

* **/art/**: Original and upscaled artwork for the collection.
* **/3d-peeperz/**: 3D model files and previews.
* **/emojis-and-stickers/**: Peeperz-themed emojis and stickers.
* **/misc/**: Miscellaneous images and banners.
* **/metadata/**: Collection metadata (`peeperz.json`).
* **/python-scripts/**: Utility scripts for managing and processing the collection (e.g., resizing images, calculating rarity).
* **/rarity/**: Rarity rankings and trait statistics.

## Key Files

* `metadata/peeperz.json`: Main metadata file for all collection items.
* `rarity/peeperz-rarity-ranks.txt`: Ranked list of all Peeperz by rarity.
* `rarity/peeperz-traits-stats.txt`: Trait frequency and rarity statistics.
* `python-scripts/resize-peeperz-easily.py`: Script to batch resize images and GIFs.
* `python-scripts/calculate-rarity.py`: Script to calculate and update rarity scores.

## Links

* [Website](https://peeperz.wtf)
* [Twitter](https://x.com/peeperz_eths)
* [Discord](https://discord.gg/QrwmU85Kw8)

## Getting Started

1. Clone the repository.
2. Install dependencies for Python scripts:

   ```bash
   pip install pillow
   ```

---

## Rarity Calculation Script

This repository includes a Python script (`calculate-rarity.py`) that computes and updates rarity rankings for all Peeperz based on their trait frequencies. Each run auto-increments a `newX` suffix on all outputs. The script performs the following tasks:

1. **Input Path Resolution**

   * Prompts in the console for a metadata JSON path, accepting either a full or relative path (including use of `~`).
   * Attempts to locate the file directly or relative to the project root (one level above the `python-scripts` folder).

2. **Item Extraction**

   * Reads the JSON and extracts items from any of these keys (in order): a top-level list; `collection_items`; or `items`.
   * Exits with an error if no items are found.

3. **Honorary & "1 of 1" Detection**

   * Scans every item's `item_attributes` list.
   * If any `trait_type` equals (case-insensitive) `honorary`, or equals `1 of 1`, that item is flagged for **rank 1**.
   * All other items will be ranked by rarity score.

4. **Trait Frequency Counting**

   * Tallies how often each `(trait_type, value)` pair appears (excluding any existing `rarity` attributes) across all items.
   * Maintains frequencies for later score calculation.

5. **Rarity Score Calculation**

   * For each NFT, calculates a per-trait score:
     $\text{score}_{trait} = \frac{N}{\text{frequency of that trait}}$
     where \$N\$ is the total number of items.
   * Sums all trait scores to get a total rarity weight per NFT.

6. **Rank Assignment**

   * Sorts all items in descending order of summed rarity weight.
   * Assigns **rank = 1** to every honorary or "1 of 1" item.
   * For the remaining items, assigns ranks starting at 2 in sorted order.

7. **Output Generation & Versioning**

   * **Updated Metadata JSON**: Writes a new JSON named `{base_name}-newX.json` alongside the input file, preserving the same directory.

     * All items gain or update a `rarity` attribute with the integer rank.
   * **Rarity Rankings File**: Creates `rarity/{base_name}-rarity-ranks-newX.txt`.

     * Lists each NFT in ascending rank order (honorary & "1 of 1" first), showing:

       ```
       Rank 1 - peeperz #1234 | Rarest trait = <trait_type> - <value> | Link: https://marketplace.peeperz.com/details/<ethscription_id>
       ```
   * **Traits Statistics File**: Creates `rarity/{base_name}-traits-stats-newX.txt`.

     * First lists any `(trait_type, value)` pairs whose `trait_type` is `honorary` or `1 of 1` (sorted by rarity score), followed by all other traits sorted by rarity score.
     * Each line shows:

       ```
       <trait_type> - <value> | rarity score = <score> | frequency = <count> / <total_items>
       ```

> **Notes on Versioning**
>
> * The script checks existing files in the same directory for the pattern `*-new(\d+).json` (or `.txt`).
> * It automatically increments the highest existing version number by 1 for the next run.
> * On a first run, if no prior `newX` exists, it uses `new1`.

### Usage

1. Place your original metadata JSON anywhere (e.g., `metadata/peeperz.json`).
2. From within `python-scripts/`, run:

   ```bash
   python calculate-rarity.py
   ```
3. When prompted, enter the path to the JSON (absolute or relative). For example:

   ```bash
   Enter path to metadata JSON file: ../metadata/peeperz.json
   ```
4. After completion, you will see three new files:

   * `metadata/{base_name}-newX.json`
   * `rarity/{base_name}-rarity-ranks-newX.txt`
   * `rarity/{base_name}-traits-stats-newX.txt`

### Example

If your input is `metadata/peeperz.json`, and there are no previous `newX` files, running the script generates:

```
metadata/peeperz-new1.json
rarity/peeperz-rarity-ranks-new1.txt
rarity/peeperz-traits-stats-new1.txt
```

1. `peeperz-new1.json` is identical to the original but with each item’s `rarity` attribute set.
2. `peeperz-rarity-ranks-new1.txt` lists rank 1 items (honorary or "1 of 1"), then others by ascending rank.
3. `peeperz-traits-stats-new1.txt` lists `honorary`/`1 of 1` traits first, then all other traits by rarity.

---

*Script maintained by the mfpurrs/Peeperz team.*
