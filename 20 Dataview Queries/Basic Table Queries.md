---
description: Showcase basic syntax of TABLE queries
topics:
  - basics
---
#dv/table #dv/from  #dv/where #dv/sort #dv/groupby  

# Basic Table Queries

## Basic 

**Show pages from a folder as table**
```dataview
TABLE
FROM "10 Example Data/games"
```

**Show pages from a tag  as table**
```dataview
LIST
FROM #type/books 
```

**Combine multiple tags**
```dataview
TABLE
FROM #dvjs/el OR #dv/min 
```

**Combine multiple folders**
```dataview
TABLE
FROM "10 Example Data/books" OR "10 Example Data/games"
```

**Combine tags and folders**
```dataview
TABLE
FROM "10 Example Data/games" AND #genre/action  
```

**List all pages**

> [!attention] Add `dataview` to code block
> The output of this is pretty long. If you want to see it, add `dataview` to the code block - like on the examples above!
> Please note: There needs to be a **space** behind `TABLE` to see results!

```
TABLE 
```


## Variants

### Show pages from a certain author

```dataview
TABLE
FROM #type/books 
WHERE author = "Conrad C"
```

### Show pages and additional information

```dataview
TABLE author, pagesRead, totalPages
FROM #type/books
```

### Show only meta data information and no file link

```dataview
TABLE WITHOUT ID source, time, ingredients
FROM "10 Example Data/food"
WHERE source
```

### Group list elements

> [!info] New "first level"
> When grouping tables, your "first level" changes. Without grouping, the first level are the file links you get back from your `FROM/WHERE` statement, so they automatically get displayed if you give no additional information to the `TABLE` command.
> After grouping though, your _groups_ become the first level, meaning that without adding an additional information you'll see the groups in your table instead of the file links. To still see the file names, you need to add them as a column. Find out more about this in [[How GROUP BY works]].

**Without additional columns**
```dataview
TABLE 
FROM "10 Example Data/books"
GROUP BY author
```

**With additional columns**
```dataview
TABLE rows.file.link, rows.pagesRead
FROM "10 Example Data/books"
GROUP BY author
```

### Customize table headers

**Of additional columns**
```dataview
TABLE contacts.phone AS "Phone Number", contacts.mail AS "E-Mail"
from "10 Example Data/people"
```

**Of the first (link/group) header without grouping**

```dataview
TABLE WITHOUT ID file.link AS "Game", developer, price
FROM "10 Example Data/games"
```

**Of the first (link/group) header with grouping**

```dataview
TABLE WITHOUT ID key AS "Author", rows.file.link AS "Books"
FROM "10 Example Data/books"
GROUP BY author
```

### Sort tables

```dataview
TABLE author
FROM "10 Example Data/books"
SORT author
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