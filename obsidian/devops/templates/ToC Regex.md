1. Generate a ToC with **Markdown All in One** extention of Visual Code
2. Replace the ToC with Regex: 

Regex:
(.*- )\[(.*)\].*

Replacement:
$1[[#$2]]

