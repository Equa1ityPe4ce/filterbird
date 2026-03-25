# Fork of FilterBird

This is a fork of the original FilterBird project by BetweenWalls. This fork is intended to integrate FilterBird into a launcher to ease filter preview capabilities.

**Disclaimer**: _This fork may not reflect the exact behavior of item filters in-game. The original FilterBird project is no longer actively maintained, and some of the features or displays may be out of sync with the latest in-game updates._

**Web Usage**: FilterBird can be used directly in a browser via the `fromweb` URL parameter.

To open with an empty editor (paste your filter manually):
```
https://equa1itype4ce.github.io/filterbird/index.html?v=PD2&fromweb=true
```

To automatically load a filter from a remote URL, provide the URL-encoded address as the `fromweb` value:
```
https://equa1itype4ce.github.io/filterbird/index.html?v=PD2&fromweb=https%3A%2F%2Fraw.githubusercontent.com%2FMaaaaaarrk%2FHiimFilter-PD2-Filter%2Frefs%2Fheads%2Fmain%2FHiim.filter
```
When a URL is provided, FilterBird will fetch the filter text from that URL and load it automatically. If the fetch fails, it falls back to the empty editor. Both modes set the page background to black and show the filter input.

**Reporting**: Found a bug or missing feature? [Open an issue on GitHub](https://github.com/Equa1ityPe4ce/filterbird/issues/new) and we'll take a look.

**Credits**
* Original Creator: BetweenWalls [REPO](https://github.com/BetweenWalls/filterbird)
* Edits for the Launcher: Maaaark
* Launcher Integration: Equa1ity
