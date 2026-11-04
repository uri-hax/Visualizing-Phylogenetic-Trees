# VennTree

VennTree is a browser-based editor for rooted phylogenetic trees. It draws a tree as a **Venn diagram**: every clade is a circle nested inside its parent's circle. You edit the tree by dragging circles, and VennTree compares trees and clades by their **tree polynomials** (Liu 2021).

**Try it:** https://uri-hax.github.io/Visualizing-Phylogenetic-Trees/

## Run it

VennTree is one self-contained file, `index.html`. There is nothing to install or build.

- **Online:** open the link above.
- **Locally:** download `index.html` and open it in a recent Chrome, Edge, Firefox or Safari.
- If your browser blocks the **copy** buttons on a `file://` page, serve the folder instead:

  ```
  python3 -m http.server 8000
  ```

  Then open http://localhost:8000.

Trees never leave your machine. The only network request is for two web fonts (Montserrat and JetBrains Mono). Without a connection, VennTree falls back to system fonts.

## Use it

VennTree has four modes, chosen at the top of the sidebar.

| Mode | What it does |
|---|---|
| **View** | Navigate a tree and inspect any clade's polynomial. |
| **Edit** | Add, rename, delete and move clades. |
| **Input** | Paste your own Newick tree, or two trees to compare. |
| **Compare** | Pick any two circles to compare their shapes. |

### Built-in data sets

The data-set menu loads any of these:

| Data set | Size | What it is |
|---|---|---|
| Slide 6 example | 5 leaves | The paper's running example (Fig. 1) |
| Deep demo | 122 leaves | A synthetic tree 41 levels deep |
| Engineered set | 309 leaves | Test clades for Compare |
| H3N2 HA | 475 strains | A Nextstrain influenza tree with branch lengths |
| Ascomycota | 619 families | A fungal taxonomy from NCBI Taxonomy, via phyloT |
| Two trees | 28 taxa | ML and Bayesian trees for the same taxa |
| Blank canvas | — | Build a tree from scratch |

### Keyboard and mouse

Press **?** in the app for this list.

| Action | Key or gesture |
|---|---|
| Zoom into a circle / go up a level | click / click outside or **Esc** |
| Pan / zoom | drag / scroll or **+** **−** |
| Fit the tree (press again to skip single-child chains) | **F** |
| Switch between the Venn diagram and the node-link tree | **T** |
| *Edit:* add a child / add a sibling | **Tab** or double-click / **Enter** |
| *Edit:* rename / delete a clade | **F2** / **Del** |
| *Edit:* move a clade under another | drag it onto that circle |
| *Edit:* split a clade into a new tree | drag it to empty space |
| Undo / redo | **Ctrl/⌘ Z** / **Ctrl/⌘ ⇧ Z** |

### Appearance

The **Appearance** panel offers:

- seven colour ramps for depth, including one without hue and one that alternates lightness by level;
- four ways to draw branch lengths in the Venn view: colour, gap, rim weight and rim arc.

The node-link view is rectangular or radial, and can be drawn by depth or with branch lengths to scale. All of these change only the drawing; the polynomials depend on the tree's shape alone.

## How it works

- **Tree polynomial.** A leaf's polynomial is *x*. An internal vertex's polynomial is *y* plus the product of its children's polynomials. Two rooted trees have the same polynomial exactly when they have the same shape (Liu 2021). VennTree keeps a polynomial on every clade.
- **Edits update only what changed.** An edit changes the polynomials on the paths from the edited vertices up to the root, and no others (Theorem 2 in the paper). VennTree finds those clades by comparing shape keys, outlines them, and shows each one's change Δ*P*.
- **Comparison.** Two trees or clades are compared by:
  - cosine similarity of their polynomials;
  - normalized Manhattan distance;
  - a heatmap of their levels, nested clades or disjoint clades.

  Clades can be compared even if they share no taxa. When three or more clades are picked, VennTree maps them by multidimensional scaling and clusters them by UPGMA.
- **Large numbers.** The whole-tree polynomial is built in a Web Worker so the page stays responsive. Coefficients beyond about 10³⁰⁰ switch to log space.

## Code layout

`index.html` holds, in order:

1. the vendored **d3-hierarchy** v3.1.2 (© Mike Bostock), used for circle packing;
2. the stylesheet;
3. the page markup;
4. the HA and Ascomycota trees as Newick strings;
5. the application script.

The script opens with a numbered table of contents, sections **[01]–[26]**. Search for `[07]` or a section title to jump there.

- Sections [01]–[25] only define things.
- **[26] Chrome wiring** is the only code that runs at load time.

| Sections | Contents |
|---|---|
| [01]–[03] | Newick parsing, data sets, building the hierarchy |
| [06]–[07] | Polynomial algebra and distances |
| [08]–[10] | Layout, drawing and labels |
| [15], [23]–[25] | Editing, undo/redo and tree operations |
| [18]–[22] | Compare mode and the heatmap |
