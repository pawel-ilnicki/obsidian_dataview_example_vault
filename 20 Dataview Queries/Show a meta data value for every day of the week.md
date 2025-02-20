---
description: Show a certain value for every day of the week, i.e. a summary of the day, or what nice happened the day 
topics:
  - weekly aggregation 
  - custom output
  - date based overviews
fake:
  name: "2022-W4"
  name2: "2022-W5"
---
#dv/list #dv/from #dv/where #dv/sort #dv/string #dv/split #dv/dateformat #dv/link #dv/join #dv/table #dv/array #dv/choice #dv/typeof

# Show a meta data value for every day of the week

## Basic 

> [!attention] Usage in weekly note
> If you want to use this query in your weekly note and your weekly is named like `gggg-[W]ww`, i.e. 2022-W4, replace `fake.name` with `file.name`

```dataview
LIST summary
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name, "-W")[0] AND string(day.weekyear) = split(this.fake.name, "-W")[1]
SORT file.name
```

> [!hint] Alternative usage
> Instead of a summary of the day you could note down something nice that happened that day and remind yourself that the week was quite nice in retrospective!

## Variants

### Print weekdays instead of file names

```dataview
LIST WITHOUT ID "**" + dateformat(day, "cccc") + "**: " + summary
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name, "-W")[0] AND string(day.weekyear) = split(this.fake.name, "-W")[1]
SORT file.name
```

### Print weekdays instead of file names but still link to daily

```dataview
LIST WITHOUT ID "**" + link(file.path,  dateformat(day, "cccc")) + "**: " + summary
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name, "-W")[0] AND string(day.weekyear) = split(this.fake.name, "-W")[1]
SORT file.name
```

### Print multiple values per day

```dataview
LIST WITHOUT ID "**" + dateformat(day, "cccc") + "**: " + join(note, " | ")
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name2, "-W")[0] AND string(day.weekyear) = split(this.fake.name2, "-W")[1]
SORT file.name
```

### Print multiple values per day as table
```dataview
TABLE WITHOUT ID "**" + dateformat(day, "cccc") + "**" AS "Day" , note
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name2, "-W")[0] AND string(day.weekyear) = split(this.fake.name2, "-W")[1]
SORT file.name
```

### Print multiple values per day as table in unified look

```dataview
TABLE WITHOUT ID "**" + dateformat(day, "cccc") + "**" AS "Day" , choice(typeof(note) = "array", note, array(note)) AS "Notes"
FROM "10 Example Data/dailys"
WHERE string(day.year) = split(this.fake.name2, "-W")[0] AND string(day.weekyear) = split(this.fake.name2, "-W")[1]
SORT file.name
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