---
description: Render a year overview and color every day that matches your search criteria
topics:
  - visualization
  - year overview
---
 #dv/dataviewjs #dvjs/pages #dvjs/luxon #dvjs/el #dvjs/duration #dvjs/view

# Render a year overview for your data

## Basic 
Track activities in your dailies, i.e. if you have prayed.

```dataviewjs
const values = dv.pages('"10 Example Data/dailys"').where(p => p.praying === "yes");
const year = 2022;
const color = "green";
const emptyColor = "rgba(255,255,255,0.1)";

// == Create calendar data ==
let date = dv.luxon.DateTime.utc(year)
const calendar = [];
for(let i = 1; i <= 12; i++) {
	calendar[i] = []
}

// == Fill calendar ==
while (date.year == year) {
	const col = !!values.find(p => p.day.equals(date.startOf('day'))) ? color : emptyColor;
	calendar[date.month].push(getDayEl(date, col))

	date = addOneDay(date);
}

// == Render calendar ==
calendar.forEach((month, i) => {
	const monthEl = `<span style='display:inline-block;min-width:30px;font-size:small'>${dv.luxon.DateTime.utc(year, i).toFormat('MMM')}</span>`
	
	dv.el("div", monthEl + month.reduce((acc, curr) => `${acc} ${curr}`, ""))
})

function addOneDay(date) {
	return dv.luxon.DateTime.fromMillis(date + dv.duration("1d"))
}

function getDayEl(date, color) {
	const sizeOfDays = "12px";
	return `<span style="width:${sizeOfDays};height:${sizeOfDays};border-radius:2px;background-color:${color};display:inline-block;font-size:4pt;" title="${date.toFormat('yyyy-MM-dd')}"></span>`
}
```

## Variants

### Use a meta data field with a date instead of file.day

See on one glimpse the **start** and **finish** dates of your projects.

```dataviewjs
const values = dv.pages('"10 Example Data/projects"').where(p => p.started);
const year = 2022;
const emptyColor = "rgba(255,255,255,0.1)";

// == Fill data ==
let date = dv.luxon.DateTime.utc(year)
const calendar = [];
for(let i = 1; i <= 12; i++) {
	calendar[i] = []
}

while (date.year == year) {
	calendar[date.month].push(getDayEl(date, determineColor(date)))

	date = addOneDay(date);
}

// == Render calendar ==
calendar.forEach((month, i) => {
	const monthEl = `<span style='display:inline-block;min-width:30px;font-size:small'>${dv.luxon.DateTime.utc(year, i).toFormat('MMM')}</span>`
	
	dv.el("div", monthEl + month.reduce((acc, curr) => `${acc} ${curr}`, ""))
})

function addOneDay(date) {
	return dv.luxon.DateTime.fromMillis(date + dv.duration("1d"))
}
function getDayEl(date, color) {
	const sizeOfDays = "12px";
	return `<span style="width:${sizeOfDays};height:${sizeOfDays};border-radius:2px;background-color:${color};display:inline-block;font-size:4pt;" title="${date.toFormat('yyyy-MM-dd')}"></span>`
}

function determineColor(date) {
	const started = values.find(p => p.started?.startOf('day').equals(date.startOf('day')));
	const finished = values.find(p => p.finished?.startOf('day').equals(date.startOf('day')));
	let color = emptyColor;

	if (started && finished) {
		color = '#9959ff';	
	} else if (started) {
		color = '#ff5976'
	} else if (finished) {
		color = 'green'
	}
	
	return color;
}
```

### Add information on hover

Hover over a dot and wait a moment to see which project started or finished.

```dataviewjs
const values = dv.pages('"10 Example Data/projects"').where(p => p.started);
const year = 2022;
const emptyColor = "rgba(255,255,255,0.1)";

// == Fill data ==
let date = dv.luxon.DateTime.utc(year)
const calendar = [];
for(let i = 1; i <= 12; i++) {
	calendar[i] = []
}

while (date.year == year) {
	calendar[date.month].push(getDayEl(date, determineColor(date), createTooltip()))

	date = addOneDay(date);
	
	function createTooltip() {
		let tooltip = "";
		const vals = values.filter(p => checkDateEq(p.started, date) || checkDateEq(p.finished, date))
		for (let val of vals) {
			tooltip += `${val.file.name} `
		}
		return tooltip;
	}
}

// == Render calendar ==
calendar.forEach((month, i) => {
	const monthEl = `<span style='display:inline-block;min-width:30px;font-size:small'>${dv.luxon.DateTime.utc(year, i).toFormat('MMM')}</span>`
	
	dv.el("div", monthEl + month.reduce((acc, curr) => `${acc} ${curr}`, ""))
})

function addOneDay(date) {
	return dv.luxon.DateTime.fromMillis(date + dv.duration("1d"))
}
function getDayEl(date, color, hoverInfo) {
	const sizeOfDays = "12px";
	return `<span style="width:${sizeOfDays};height:${sizeOfDays};border-radius:2px;background-color:${color};display:inline-block;font-size:4pt;" title="${hoverInfo}"></span>`
}

function checkDateEq(date1, date2) {
	if (!date1 || !date2) return false
	return date1.startOf('day').equals(date2.startOf('day'))
}

function determineColor(date) {
	const started = values.find(p => p.started?.startOf('day').equals(date.startOf('day')));
	const finished = values.find(p => p.finished?.startOf('day').equals(date.startOf('day')));
	let color = emptyColor;

	if (started && finished) {
		color = '#9959ff';	
	} else if (started) {
		color = '#ff5976'
	} else if (finished) {
		color = 'green'
	}
	
	return color;
}
```

### Use a intensity to display different values

```dataviewjs
const values = dv.pages('"10 Example Data/dailys"').where(p => p.wellbeing?.mood);
const year = 2022;
const emptyColor = "rgba(255,255,255,0.1)";

// == Fill data ==
let date = dv.luxon.DateTime.utc(year)
const calendar = [];
for(let i = 1; i <= 12; i++) {
	calendar[i] = []
}

while (date.year == year) {
	calendar[date.month].push(
	getDayEl(
		date, 
		determineColor(date)))

	date = addOneDay(date);
}

// == Render calendar ==
calendar.forEach((month, i) => {
	const monthEl = `<span style='display:inline-block;min-width:30px;font-size:small'>${dv.luxon.DateTime.utc(year, i).toFormat('MMM')}</span>`
	
	dv.el("div", monthEl + month.reduce((acc, curr) => `${acc} ${curr}`, ""))
})

function addOneDay(date) {
	return dv.luxon.DateTime.fromMillis(date + dv.duration("1d"))
}

function getDayEl(date, color) {
	const sizeOfDays = "12px";
	return `<span style="width:${sizeOfDays};height:${sizeOfDays};border-radius:2px;background-color:${color};display:inline-block;font-size:4pt;" title="${date.toFormat('yyyy-MM-dd')}"></span>`
}

function determineColor(date) {
	const page = values.find(p => p.day.startOf('day').equals(date.startOf('day')));
	if (!page) return emptyColor;


	let opacity = (page.wellbeing.mood / 4) ;
	return `rgba(177, 200, 51, ${opacity})`;

}
```

### Use as a view file for reusability

![[What is#^dv-view]]

**Simple case** - input pages to display, the year to render and an "active" color 
```dataviewjs
await dv.view("00 Meta/dataview_views/year_overview", 
	{
		pages: dv.pages('"10 Example Data/dailys"').where(p => p.praying === "yes"),
		year: 2022,
		color: "green"
	})
```

**Complex case** - Provide functions to determine color and tooltip. Both function will be called with the date to fill as argument and need to give back a string.

```dataviewjs
const pages = dv.pages('"10 Example Data/dailys"').where(p => p.wellbeing?.mood);

await dv.view("00 Meta/dataview_views/year_overview", 
	{
		pages: pages,
		year: 2022,
		color: determineColor,
		tooltipFn: generateTooltip
	})

function determineColor(date) {
	const page = pages.find(p => p.day.startOf('day').equals(date.startOf('day')));
	if (!page) return "rgba(9, 99, 199, 0.15)";

	let opacity = (page.wellbeing.mood / 4) ;
	return `rgba(177, 200, 51, ${opacity})`;

}

function generateTooltip(date) {
	const page = pages.find(p => p.day.startOf('day').equals(date.startOf('day')));
	if (!page) return date.toFormat('yyyy-MM-dd');

	return `${page.file.name}: ${page.wellbeing?.mood ?? ''}`
}
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