---
description: Show a calender that marks all days with uncompleted tasks
topics:
  - task tracking
---
#dv/calendar #dv/from #dv/flatten #dv/where #dv/all #dv/map #dv/any

# Mark days that have unfinished todos

> [!info]
> You'll need to go back to Januray/Februrary 2022 to see the data.

## Basic 

```dataview
CALENDAR file.day
FROM "10 Example Data/dailys"
FLATTEN all(map(file.tasks, (x) => x.completed)) AS "allCompleted"
WHERE !allCompleted
```

> [!tip]
> When you try to write complex calendar queries, write a TABLE query first to make sure your query returns the results you're expecting.

```dataview
TABLE file.day, allCompleted
FROM "10 Example Data/dailys"
FLATTEN all(map(file.tasks, (x) => x.completed)) AS "allCompleted"
WHERE allCompleted
```
## Variants

### If you use custom task status and want to see all without a status

```dataview
CALENDAR file.day
FROM "10 Example Data/dailys"
FLATTEN any(map(file.tasks, (x) => x.status = " ")) AS "anyEmpty"
WHERE anyEmpty
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