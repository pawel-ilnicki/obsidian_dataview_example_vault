---
description: List all your assignments and highlight those that are overdue by coloring them in red
topics:
  - custom output
  - highlight specific values
---
 #dv/TABLE #dv/SORT #dv/choice #dv/date

# List pages with due date and color those overdue

## Basic 

```dataview
TABLE choice(due < date(today), "<span style='color: red;'>" + due + "</span>", due) AS "Due"
from "10 Example Data/assignments"
SORT due asc
```

## Variants

### Add an additional emoji in front of the link 

Useful when you display more meta data or sort after another property.

```dataview
TABLE WITHOUT ID choice(due < date(today), "🛑 " + file.link, " ⚫ " + file.link) AS "Assignment", received, class, choice(due < date(today), "<span style='color: red;'>" + due + "</span>", due) AS "Due"
from "10 Example Data/assignments"
SORT class asc
```
---
%% === end of query page === %%
> [!help]- Similar Queries
> Maybe these queries are of interest for you, too:
> ```dataview
> LIST
> FROM "20 Dataview Queries"
> FLATTEN topics as flattenedTopics
> WHERE contains(this.topics, flattenedTopics)
> AND file.name != this.file.name
> ```

```dataviewjs
const inlinksFromUseCases = dv.current().file.inlinks.filter(link => link.path.contains("33 Use Cases"));

const header = `> [!info] Part of Use Cases`;

if (inlinksFromUseCases.length > 1) {
	const list = inlinksFromUseCases.array().reduce((acc, curr) => `${acc}</br> - ${curr}`,"")

	dv.span(`${header}
    > This query is part of following use cases:
    > ${list}
    > 
	`)
} else if (inlinksFromUseCases.length === 1) {
	dv.span(`${header}
    > This query is part of use case ${inlinksFromUseCases[0]}.
	`)
}
```