**To convert only links with knowladge content, inlcude only `*.md` files and exclude `Regex rules.md`**

# Table of Content

1. Generate a ToC with GitHub internal links using **Markdown All in One** extention of Visual Code
2. Replace the ToC with Regex:

# Converting Obsidian's (Wikilinks / MD) and GiHub's (MD) internal links

## Obsidian Wiki to MD

**It hast to be run one by one in the order.**

1. extract an alias from `[[note#header|alias]]` to `[alias](note#header)`

```
\[\[([^\|\]]*)\|([^\]]+)\]\]

[$2]($1)
```

2. extract an alias copying a header from `[[note#header]]` to `[header](note#header)`

```
\[\[([^#\]]*#([^\]]+))\]\]

[$2]($1)
```

3. extract an alias copying a note from `[[note]]` to `[note](note)`

```
\[\[(.*?)\]\]

[$1]($1)
```

4. replace whitespaces with `%20`

```
(\[[^\]]+\]\((?!http)[^\)]*?) ([^\)]+?)\)

$1%20$2)
```

5. add `.md` to note in `[label](note#header)`

```
((\[[^\]]+\]\((?!http)[^#\)]*?)([^#\)])+)

$1.md
```

6. revert images with `.png` url removing `.md` from url

```
.png.md\)

.png)
```

7. add an attechements location to inage url

```
(\[[^\]]+\]\()((?!http)[^#\)]*).png

$1../attachments/$2.png
```

## Obsidian MD to GitHub MD

- make obsidian internal links `[]()` github ones `[]()`
- `$20` -> `-`

```
(\[[^\]]+\]\((?!http)[^\)]*?)%20([^\)]+?)\)

$1-$2)
```

## GitHub MD to Obsidian MD

- make github internal links `[]()` obsidian ones `[]()`
- `-` -> `$20`

```
(\[[^\]]+\]\((?!http)[^\)]*?)-([^\)]+?)\)

$1%20$2)
```

## GitHub MD to Obsidian Wiki

```
(.*- )\[(.*)\].*

$1[[#$2]]
```
