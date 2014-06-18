# GuideGuide Notation

GuideGuide’s grid form is a great tool, but it is somewhat limited in what it can do. To give you more flexibility with your grids, GuideGuide includes a feature called GuideGuide Notation, a special language that allows you to give instructions to GuideGuide, telling it where you would like to put guides within your document or selection.

## Grids

> &lt;commands&gt; ( &lt;options&gt;, &lt;first offset&gt; | &lt;width&gt; | &lt;last offset&gt; )

A grid is a collection of guides and gaps across a single dimentional plane. GuideGuide will split the string into an array of guides and gaps and iterate through them, following them like instructions. Starting at 0, for each gap, GuideGuide will advance its insertion point by the value of the gap. When GuideGuide encounters a guide, it will place a guide at the current location of the insertion point. This will continue until all guides and gaps have been parsed.

GuideGuide takes into acount the size of the document or selection when calculating percentages, wildcards, and fills.

Each guide or gap must be separated by a space character. Newlines are used to define multiple grids in one string.

It is possible to change the way GuideGuide renders the grid by specifiying options at the end of the grid, within parentheses. A width for the grid can be specified, as well as an offset to start rendering the grid. Whitespace in the options are is ignored.
e.

#### Examples

- a guide at the top, right, bottom, and left of the document.

  ```
  | ~ | (h)
  | ~ | (v)
  ```


- a three row vertical grid

  ```
  | ~ | ~ | ~ | ( h )
  ```


- a one hundred pixel horizontal grid with a ten pixel left margin, ten pixel right margin, and a twenty pixel column centered in the middle

  ```
  | 10px | ~ | 20px | ~ | 10px| ( v, 100px )
  ```

- a grid with a left side bar with 10px on either side, and a five columns filling the gap.

  ```
  | 10px | 200px | 10px | ~ | ~ | ~ |
  ```

## Unit objects

> &lt;value&gt;&lt;unit&gt;

Unit objects are value-unit pairs that indicate a measurement.

#### Examples

- `72px`
- `1in`
- `2.54cm`
- `25.4mm`
- `72pts`
- `6pica`
- `100%`

## Guides

Guides are represented by a pipe `|`. These tell GuideGuide to place a guide at the current insertion point.

## Gaps

Gaps are unit objects or variables combined with multipliers to define spaces between gaps.

### Arbitrary gaps

> &lt;value&gt;&lt;unit&gt;*&lt;multiplier&gt;

An arbitrary gap is represented by a Unit Object and an optional multiplier. Arbitrary gaps are the width of the unit specified. Arbitrary can be positive or negative. Due to this, it is possible to traverse backwards and forwards.

#### Examples

- three ten pixel columns

  `| 10px | 10px | 10px|`


- one half inch column, one inch column, one half inch column

  `| .5in | 1in | .5in |`

### Wildcard gaps

A wildcard gap is represented by a tilde `~`. Any area within a grid that remains after all of the arbitrary gaps have been calculated will be evenly distributed amongst the wildcards present in a grid.

Due to their flexible nature, wildcards can be used to position a grid. When a single wildcard is placed to the left of a GGN string, it will force the grid to render on the right side. Similarly, a GGN string with wildcards on either end will be centered.

#### Examples

- A guide on the left and right side of the document or selection.

  `| ~ |`

- A three column grid

  `| ~ | ~ | ~ |`

### Variables

Variables allow you to define and reuse collections of gaps within a grid. Variables are composed of a definition and a call.

#### Define

> $&lt;id&gt; = &lt;gaps&gt;

A variable definition is represented by a dollar sign `$`, an optional id, an equals sign, and then a collection of gaps and guides separated by spaces.

While it is possible to define a variable that contains no guides, this won't often be useful as the results of the variable will not be visible (since it contains no guides).

#### Call

> $&lt;id&gt;*&lt;multiplier&gt;

A variable call is represented by a dollar sign `$`, an optional id, and an optional multiplier. Anywhere a variable call occurs GuideGuide will replace its contents with the contents of its variable definition. A variable must be defined before it is called.

#### Example

```
$ = ~ |
| $*3
```

expands to:

```
| ~ | ~ | ~ |
```

a three column grid


### Multiples and fills

Arbitray, wildcard, and variable gaps can accept a final modifier that will duplicate that gap the number of times specified. These are most helpful when used with variables, as it is possible to specify both gaps and guide together. Multiples and fills can be specified on gaps, but since the result of the mutiplied gap is not visible, their usefulness is rare.

#### Multiple

A multiple is represented by an asterisk `*` followed by a number. The gap will be recreated sequentially the number of times specified by the multiple

#### Examples

- Two thirds column, one third column

  `| ~*2 | ~ |`

- A three column grid with ten pixel gutters

  ```
  $ =  ~ | 10px |
  | $*2 ~ |
  ```  

#### Fill

A fill is represented by a asterisk `*` folowed by nothing and is a gap that will be recreated squentially until it fills the remaining space in the grid. This is useful for cases such as creating a baseline grid, or filling a space with as many columns and gutters of a certain width as will fit.

- A sixteen pixel baseline grid

  ```
  $ = 16px |
  | $* ( h )
  ```


## Grid Options

> (&lt;modifiers&gt;,  &lt;adjustments&gt;)

Optional values to modify how the grid is created. They are wrapped in parens and broken into two sections separated by a comma.

#### Examples

- `(vfp, ~|~)`  
  A grid of vertical guides, with "first" remainder distribution, in pixel mode. The grid is centered.

### Orientation

Determines the orientation of the guides in the grid.

#### Values:

- `h` *(default)* horizontal

- `v` vertical

### Remainder pixel distribution

Determines to which wildcards GuideGuide adds remainder pixels when the columns do not divide equally into the total width of the grid area.

#### Values:

- `f`*(default)* first (left/top)

- `c` center

- `l` last (right/bottom)

### Calculation

Determines whether GuideGuide is strict about integers when calculating pixels

#### Values:

- `n` *(default)* normal

- `p` pixel specific

### Grid adjust

> &lt;left offset&gt; | &lt;width&gt; | &lt;right offset&gt;

A string similar to Grid notation that specifies the left and right offsets and width of the grid, separated by pipes (which represent the edges of the grid).

Width is defined by enclosing a unit object in pipes. The tilde `~` can be used similarly to the way wildcards are used.

#### Examples:

- `(v, |100px|)`  
  A one hundred pixel wide grid.

- `(v, |~|)`  
  A full width grid (default)

Position works similarly to how CSS works. Think of the `~` as "auto". To define the left or right offset, place a `~` or unit object on either side of the pipes.

#### Examples:

- `(v, 20px|100px|)`  
  A one hundred pixel wide grid, twenty pixels from the left side.

- `(v, |100px|20px)`  
  A one hundred pixel wide grid, twenty pixels from the right side.

- `(v, 20px|100px|20px)`  
  A one hundred pixel wide grid, twenty pixels from the left side (the right offset is ignored if a left and right offset is specified with a defined width).

- `(v, 20px|~|20px)`  
  A grid with a width that is 40px less than the width of the document, with 20px space on either side.

- `(v, ~|100px|)`  
  A right aligned, one hundred pixel wide grid.

- `(v, ~|100px|~)`  
  A centered, one hundred pixel wide grid.

For width to be specified, it **must** have a pipe on either side. If only one pipe between two values is specified, the values will be treated as left and right offsets. If a third pipe exists, it and anything after it is ignored.

#### Examples:

- `(v, ~|~)`  
  A centered grid.

## Errors

GuideGuide String Notation errors will be denoted in curly brackets. Directly following a bracketed error will be a set of brackets containing a comma separated list of error IDs. Explanation of the errors will be printed below the grid.

```
| 10px | { 10foo [1]} | 10px|

# 1. Unrecognized unit type
```