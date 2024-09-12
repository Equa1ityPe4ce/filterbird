# Fork of FilterBird

This is a fork of the original FilterBird project by BetweenWalls. This fork is intended to integrate FilterBird into a launcher to ease filter preview capabilities.

**Disclaimer**: _This fork may not reflect the exact behavior of item filters in-game. The original FilterBird project is no longer actively maintained, and some of the features or displays may be out of sync with the latest in-game updates._

**Known Issues**:
* Nothing special is done to display item notifications or minimap icons
* All PD2 items use the same attributes as those in the PoD version (with the exception of PD2-specific equipment and pointmod attribute names)
* PD2-specific equipment items and runeword options aren't hidden if the PoD version is selected (and vice versa)
* Unique and set items cannot have their attributes customized
* Crafted items don't have predetermined affixes
* Wirt's Leg and quest weapons cannot be customized
* Item condition issues:
  * "CHSK" codes for skill charges and "OS" codes for oskills are unimplemented
  * PRICE is not updated automatically
  * Unimplemented PoD-specific conditions: AREALVL (may be others due to poor documentation, please report them)
  * Unimplemented PD2-specific conditions: PREFIX, SUFFIX, MAPID
  * STAT360 (item corruption) is only partially implemented - it checks whether a corruption exists, but not which corruption
  * There may be some numbered "STAT" codes which aren't implemented - please report them
* Many PD2 changes after s6 have not been implemented, or have only been partially implemented

**Reporting**: Please Message Equa1ity in discord with issues

This version may contain discrepancies from the original game's filter display. If any problems arise, please check the following links for issues and solutions.

    Original FilterBird GitHub

Please also visit the original repository for credit and more information.
Original [REPO](https://github.com/BetweenWalls/filterbird) from the legend BetweenWalls.

**Credits**
* Origional Creator: BetweenWalls
* Edits for the Launcher: Maaaark
* Launcher Integration: Equa1ity