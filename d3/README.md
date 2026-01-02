# D3

Keeping track of learning D3 here.

Using [Interactive Data Visualization for the Web](https://scottmurray.org/work/d3-book-2e) by Scott Murray to start!
* Note that I am using D3 v7 while the book refers to v4

# How to Run
```
cd src
python -m http.server 8888
```

Then go to `http://localhost:8888`

# Interactive Data Visualization for the Web

## Chapter 2: Introducing D3

p7
* Primary author of D3 is [Mike Bostock](https://bost.ocks.org/mike/)
* Entirely open-source on [GitHub](https://github.com/d3) with other dedicated contributors
* [D3 homepage](https://d3js.org/)

### What it Doesn't Do

p9
* D3 does not hide your original data
  * D3 is run on client-side, so the data to be visualized is sent to the client's computer
  * Can extract data off a graph using [DataThief](https://datathief.org/)
* Don't use D3 if the the data cannot be shared - then what is the point?

### Origins and Context
p10
* [Technical design and philosophy behind D3 (InfoVis paper)](http://vis.stanford.edu/files/2011-D3-InfoVis.pdf)

### Alternatives
p10
* D3 cannot be used on *really* old browsers
* If you need something simple quick and no time to code it up
* Alternatives for: simple charts, connected graphs, geomapping (geographic data)
    * See book for example libraries

p12
* Other tools for drawing free-form vector graphics
    * [paper.js](http://paperjs.org/) - see their examples!
    * [p5.js](https://get-lauren.net/p5-js) (led by Lauren McCarthy)
    * ... and more (see book)

p13
* Rendering 3D: three.js
* Libraries built on top of D3

p14
* More specialized tools that uses or used with D3
    * [Crossfilter](https://github.com/crossfilter/crossfilter) for working with large, multivariate datasets
    * ... a bunch more

## Chapter 3: Technology Fundamentals

* A nice explanation of how the web/browsers work
* Browser developer tools (Chrome: (three dots menu on top-right) -> More Tools -> Developer Tools)
* HTML/CSS/Javascript review

## Chapter 4: Setup

### Downloading D3
Using the updated [D3 docs](https://d3js.org/getting-started#d3-in-vanilla-html) instead.

JavaScript terminology:
* ES Module (ECMA module) - official standard format of Javscript modules (to be able to share/import JavaScript)
* UMD (Universal Module Definition) is another JS module format before a standard existed
  
## Chapter 5: Data

### Generating Page Elements

p72
* [D3 API reference](https://d3js.org/api) to know args/return types

### Binding Data
p78
* `d3.csv()`, `d3.json()`
    * When reading in CSV, all values are stored as a string (ex. integers and floats are stored as a string)
* [Mr. Data Converter](https://shancarter.github.io/mr-data-converter/): convert CSV to JSON/other formats
 
 #### Please Make Your Selection
* `enter()` method:
```js
var dataset = [5, 10, 15, 20, 25];

d3.select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("p")
    .text("A new paragraph! すげーー");
```

A breakdown of the code above:
* `.selectAll("p")`: Gets a reference to all `"p"` elements in the HTML DOM, even those that *do not exist yet*
* `.data(dataset)`: Counts and parses the data values
  * Method calls after this call will occur `len(dataset)` times
* `.enter()`: This looks at the current DOM and the dataset, and creates new *placeholder elements* in the DOM if there are more data items than DOM elements (from the `.selectAll("p")`), and returns a reference to the *new* DOM elements (not existing ones)

I had to add this to `index.html` for the Developer Tools console to use D3:
```html
<head>
    <meta charset="utf-8">
    <!--Access D3 in the console-->
    <script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
</head>
```

If we then run `d3.selectAll("p")` in the JS console after reloading `index.html`, we can see that each `p` element now has a `__data__` field with a data element from `dataset` binded to the `p` element:
```
> d3.selectAll("p")
Vn {_groups: Array(1), _parents: Array(1)}
    _groups: Array(1)
        0: NodeList(5)
            0: p
                __data__: 5
                ...
```

This data does not exist in the DOM, but exists in memory as `__data__`.

#### Using Your Data
Replace the last line in the code snippet above to add a callback function:
```js
select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("p")
    .text(function(data) {
        var s = "The number " + data + ", すげーー";
        return s;
    });
```

<img src="images/binding-data-5-elements.png" width="150">

Whenever we call `data()`, we can create an anonymous function that accepts the data element as an argument.

#### Beyond Text
We can modify HTMl/CSS properties with `attr()` and `style()`.

```js
.style("color", function(data) {
    if (data > 15) {
        return "red";
    } else {
        return "black";
    }
});
```

<img src="images/change-color-based-on-threshold.png" width="150">

## Chapter 6: Drawing with Data
p91
* Create a style for the class `"bar"` in a separate file `style.css`

```css
div.bar {
    display: inline-block;
    width: 20px;
    height: 75px;
    background-color: teal
}
```
* Move CSS to a separate file and import it in `index.html`:
```html
<link rel="stylesheet" href="style.css">
```
* Then create a `div` with class `"bar'`:
```html
<div class="bar"></div> 
```


* `.classed("bar", true)`: Can apply a style of a class called "bar" to an element (use `false` to remove the style)

#### Back to the Bars
We can apply the `"bar"` style class to our data elements:
```js
var dataset = [5, 10, 15, 20, 25];
d3.select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("div")
    .attr("class", "bar");
```

<img src="images/apply-class-bar-to-dataset.png" width="100">

Modify the height of the `div` based on the data element:
```js
.attr("class", "bar")
.style("height", function(d) {
    return d + "px";
});
```

<img src="images/bar-height-based-on-data-element.png" width="150">

Scale the height and add some margin between bars:
```js
.style("height", function(d) {
    var barHeight = d*5; // scale the height for visibility
    return barHeight + "px";
});
```
```css
div.bar {    
    /* ... */
    margin: 2px;
}
```

Yay, a decent-looking bar chart!

<img src="images/bar-scaled-margin-2px.png" width="150">

We can inspect the final HTML that is generated:

<img src="images/inspect-bars-console.png" width="500">

### The Power of `data()`
p93

The `data()` call automatically goes through all the data.

Adding more data items and rules:
```js
var dataset = [
    4, 99, 21, 2, 34, 70, 62, 3, 55, 8
];
d3.select("body").selectAll("p")
    .data(dataset)
    .enter()
    .append("div")
    .attr("class", "bar")
    .style("height", function(d) {
        var barHeight = d*5; // scale the height for visibility
        return barHeight + "px";
    })
    .style("background-color", function(d) {
        if (d > 50) {
            return "#cc5555"; // a nicer red
        } else {
            return "teal";
        }
    });
```

<img src="images/more-bars-and-rules.png" width="200">

"*The data is driving the visualization - not the other way around.*"

#### Random Data

Generate random data values and graph them:

```js
var dataset = []
for (var i=0; i<25; i++) {
    // Generate a random number between 0 and 100
    var d = Math.random() * 100;
    dataset.push(d);
}
```

<img style="display: inline;" src="images/bars-random-data-0.png" width="200">
<img style="display: inline;" src="images/bars-random-data-1.png" width="200">
<img style="display: inline;" src="images/bars-random-data-2.png" width="200">
<img style="display: inline;" src="images/bars-random-data-3.png" width="200">

### Drawing SVGs

* SVG format (in the DOM) look like HTML syntax
* D3 `append()` and `attr()` can also be applied to SVGs

#### Create the SVG

Can create an empty SVG element in the DOM:

```js
var svg = d3.select("body").append("svg");
```

With Developer Tools we can see that the element was created:

<img src="images/create-svg-element.png" width=150/>

We can reference the SVG element and apply attributes directly:
```js
var width = 500;
var height = 50;
var svg = d3.select("body")
    .append("svg")
    .attr("width", width)
    .attr("height", height);
```

<img src="images/create-svg-element-attributes.png" width=250/>


#### Data Driven Shapes

```js
var dataset = [5, 10, 15, 20, 25];

// Create circle elements inside the SVG element
svg.selectAll("circle") // returns a reference to empty Circle elements
    .data(dataset) // Binds each data item in `dataset` to each Circle element
    .enter() // Return a reference to the new element
    .append("circle"); // Add circle to the DOM
```

Add styles based on the data value binded to each circle element
```js
circles.attr("cx", function(d, i) { // d=data element, i=index
        return (i*50) + 25;
    })
```

D3 provides the bounded data `d` and the index `i` within the dataset (`d` and `i` are arbitrary variable names).

[Documentation on all SVG attributes](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Attribute). Played around with some:

```js
circles.attr("cx", function(d, i) {
        return (i*50) + 25;
    })
    .attr("cy", height/2)
    .attr("r", function(d) {
        return d;
    })
    .attr("fill", function(d) {
        if (d%10 == 0) {
            return "#db7fef";
        } else {
            return "#91ebf7";
        }
    });
```

Circle attributes [(full docs)](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Element/circle)
* `cx`: x-coordinate of the center of the circle in the SVG element
* `cy`: y-coordinate of the center of the circle in the SVG element
* `r`: radius
* `fill`: color of the circle

🫧

<img src="images/filled-circles.png" width=200/>

### Making a Bar Chart

Create rectangles inside of an SVG object in the DOM:

```js
// Create an SVG element inside the DOM body element
var width = 500;
var height = 100;
var svg = d3.select("body")
    .append("svg")
    .attr("width", width)
    .attr("height", height)
    .attr("style", "outline: thin solid #dedede");

// Select all the *future* rectangles that will be created
svg.selectAll("rect")
    .data(dataset)  // dataset is passed onto enter()
    .enter()        // which creates an empty reference to each data value
    .append("rect") // Creates a rectangle for each data value
    .attr("x", function(data, index) {
        return index * (width / dataset.length);
    })   // Apply the following attributes to each rectangle
    .attr("y", 0)
    .attr("width", 20)
    .attr("height", 100);
```

<img src="images/svg-bars.png" width="400">

If we adjust the bar height with the data value:
```js
.attr("height", function(data, index) {
    return data;
});
```

the bars are upside down:

<img src="images/svg-bar-heights.png" width="400">

This is because the SVG coordinate system has `(x=0, y=0)` at the top-left corner of the SVG object:

<img src="https://developer.mozilla.org/en-US/docs/Web/SVG/Tutorials/SVG_from_scratch/Positions/canvas_default_grid.png">

We want the base of the rectangle to be at the bottom of the SVG object minus the height of the bar (which is just `data`), so that it fits in the SVG object:
```js
.attr("y", function(data, index) {
    return svgHeight - data;
})
```

<img src="images/svg-bar-heights-adjusted.png" width="400">

#### Color

Adjust the color based on bar height:
```js
.attr("fill", function(data, index) {
    return "rgb(" + Math.round(data*10) + ", 0, 0)";
});
```

<img src="images/svg-bar-height-colors.png" width="400">

#### Labels

We create a `text` element in the SVG object for each dataset, and apply attributes:

```js
// Selects all *future* text that will be created
svg.selectAll("text")
    .data(dataset) // the dataset to be passed into enter()
    .enter()
    .append("text") // Creates an empty reference to a text object for this data value
    .text(function(data, index) { // Not set via attr()
        return data;
    })
    .attr("x", function(data, index) {
        // Set the label to be positioned in the middle of the bar
        let barWidth = (svgWidth / dataset.length) - barPadding;
        return index * (svgWidth / dataset.length) + (barWidth / 2); // padding
    })
    .attr("y", function(data, index) {
        return svgHeight - (data*4) + 20; // +20 padding
    })
    .attr("fill", "white")
    .attr("font-size", "12px")
    .attr("font-family", "sans-serif")
    .attr("text-anchor", "middle"); // set label to be in the middle
```

<img src="images/bars-with-labels.png" width="400">


### Making a Scatterplot

We now use 2-dimensional data:

```js
// Scatterplot dataset
var dataset = [
    [134, 223],
    [208, 117],
    [121, 55],
    [311, 211],
    [145, 131],
    [212, 17]
];
// Create the SVG element in the DOM
var svg = d3.select("body")
    .append("svg")
    .attr("width", svgWidth)
    .attr("height", svgHeight)
    .attr("style", "outline: thin solid #dedede");

// For each datapoint, we create a circle
svg.selectAll("circle") // Get a reference to all *future* circles that will be created
    .data(dataset)      // Get the dataset to be applied to each circle
    .enter()            // Pass in `dataset` to enter()
    .append("circle")   // Create the actual circle
    .attr("cx", function(data, index) {
        return data[0];
    })
    .attr("cy", function(data, index) {
        return data[1];
    })
    .attr("fill", "black")
    .attr("r", 3);
```

Just some dots... 😆

<img src="images/boring-scatterplot.png" width="400">

If we want to use the circle's size to represent the data that the circle is bounded to, use the circle's *area*, not radius.
This is because human perception associates a circle's area to its "value" - using the circle's radius would lose that relativity between circles and overexaggerate the difference between data points (as circles).

Area `A` of a circle:
```
A = πr^2
```

What do we need to set the radius `r` to in our visualization?

```
A/π = r^2
sqrt(A/π) = r
```

We use the data value of the circle to "correlate" with the area `A` in the equation.

Here we arbitrarily make circles larger if the data have a greater `y` value:
```js
.attr("r", function(data, index) {
    return Math.sqrt(data[1] / Math.PI);
});
```

<img src="images/vary-size-scatterplot.png" width="400">

#### Labels

```js
// Labels for each point in the scatterplot
svg.selectAll("text")
    .data(dataset)
    .enter()
    .append("text")
    .text(function(data, index) {
        return "(" + data[0] + ", " + data[1] + ")";
    })
    .attr("x", function(data, index) {
        return data[0];
    })
    .attr("y", function(data, index) {
        return data[1] + 21; // padding
    })
    .attr("text-anchor", "middle");
```

<img src="images/scatterplot-with-labels.png" width="400">


## Chapter 7: Scales

*"Scales are functions that map from an input domain to an output range."*

* Scales are a mathematical relationship between data
* **Axes** are a visual representation of scales
* Many types of scales: linear, ordinal, logarithmic, square root, etc...
* Use scales instead of hard-coded pixel values when displaying points

### Domain and Ranges

Given a hypothetical dataset `[1, 2, 3, 4, 5]`:
* **Input domain**: are the range of possible data values (in the example dataset, this would be 1-5)
* **Output range**: is the posslble *display values* that the data gets mapped to onto the screen
  * This is determined by the visalization designer
  * Example: the smallest value `1` gets mapped to pixel value `10px` and the largest value `5` gets mapped to pixel value `100px`.

### Normalization

With a linear scale, the above is performing *normalization*, mapping a numeric value to a value between 0 and 1.

### Creating a Scale

We can access D3's scale function with (on the console, for example):
```js
> var scale = d3.scaleLinear(); // in Javascript, functions can also be variables
> scale(2.5);
2.5
```

We need to set the domain and range of the scale:
```js
> scale.domain([100, 500]);
> scale.range([10, 350]);
```

Now input values are appropriately scaled:
```js
> scale(100)
10
> scale(200)
95
> scale(500)
350
```

We can use D3's scale function in our `attr()` when plotting points.

### Scaling the Scatterplot

We can also use `d3.min()` and `d3.max()`

```js
// Create scalesi that will ensure that the points always fit in the SVG object
// We fit the dataset values to the width and height of the SVG
var xScale = d3.scaleLinear()
    // Across the dataset, compare each value's first element with each other
    .domain([0, d3.max(dataset, function(data) { return data[0]; })])
    // Map the dataset x-values to the bounds of the SVG's width
    .range([0, svgWidth]);
var yScale = d3.scaleLinear()
    .domain([0, d3.max(dataset, function(data) { return data[1]})])
    .range([0, svgHeight]);
```

and apply the scales to the x and y values of the circles and the labels:
```js
.attr("cx", function(data, index) {
    return xScale(data[0]);
})
.attr("cy", function(data, index) {
    return yScale(data[1]);
})
```

```js
.attr("x", function(data, index) {
    return xScale(data[0]);
})
.attr("y", function(data, index) {
    return yScale(data[1]) + 20; // padding 
})
```

<img src="images/scatterplot-with-scaling.png" width="400">

We can change `yScale` to flip y-values so that larger y-values are at the top:
```js
.range([svgHeight, 0]);
```

<img src="images/scatterplot-with-scaling-y-values.png" width="400">

Add some padding in the scales so that the points completely fit in the SVG:
```js
var padding = 20;
var xScale = d3.scaleLinear()
    // Across the dataset, compare each value's first element with each other
    .domain([0, d3.max(dataset, function(data) { return data[0]; })])
    // Map the dataset x-values to the bounds of the SVG's width
    .range([padding, svgWidth - padding]);
var yScale = d3.scaleLinear()
    .domain([0, d3.max(dataset, function(data) { return data[1]})])
    .range([svgHeight - padding, padding]);
```

<img src="images/scatterplot-with-padding.png" width="400">

If we change the size of the SVG or add a new data point `[800, 400]` in the far corner, the image adjusts/scales automatically:

<img src="images/scatterplot-large-point.png" width="500">

### Other Methods

There's a bunch of other D3 scales, such as `scaleSqrt`, `scalePow`. See [the docs](https://d3js.org/d3-scale) for a full list.

We can use `scaleSqrt` to adjust the circle's area more easily:

```js
var aScale = d3.scaleSqrt()
    .domain([0, d3.max(dataset, function(data) { return data[1]; })])
    .range([0, 10]); // Range is arbitrary - what matters is that the circle areas are relative
```

```js
.attr("r", function(data, index) {
    return aScale(data[1]);
});
```

<img src="images/scatterplot-scaleSqrt.png" width="400">


### Time Scales

[Javascript `Date` object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date) represents a moment in time in Javascript.

On the console:
```js
> new Date
Date Fri Dec 19 2025 09:23:13 GMT-0500 (Eastern Standard Time)
```

Cool tangents to read about on the concept of time:
* [UTC: The World's Time Standard](https://www.timeanddate.com/time/aboututc.html)
* [moment.js](https://momentjs.com/)
* [Data Stores podcast - Rocket Science with Rachel Binx](https://datastori.es/70-rocket-science-with-rachel-binx/)

Javascript and D3 can only work with time with `Date` objects.

#### Converting strings to dates

D3 provides functions to parse strings of varying formats into `Date` objects, see the documentation on [d3-time-formats](https://d3js.org/d3-time-format).
* [timeParse](https://d3js.org/d3-time-format#timeParse)
* [timeFormatLocale](https://d3js.org/d3-time-format#timeFormatLocale)

```js
> var parseTime = d3.timeParse("%m/%d/%y")
> parseTime("12/19/25")
Date Fri Dec 19 2025 00:00:00 GMT-0500 (Eastern Standard Time)
```

We can read in a CSV file of dates `time_scale_data.csv`:
```csv
Date,Amount
12/19/25,10
12/20/25,20
12/21/25,31
12/22/25,32
12/23/25,33
12/24/25,40
12/25/25,55
```

and parse the contents to Javascript objects with converted `Date` objects and integers (recall CSV values are strings, even integers):
```js
var timeParser = d3.timeParse("%m/%d/%y");
var csvRowConverter = function(row) {
    return {
        date: timeParser(row.Date),
        amount: parseInt(row.Amount)
    };
};
// Need to reference data in the `then()` callback, as d3.csv() is async
d3.csv("data/time_scale_data.csv", csvRowConverter)
    .then(function(parsedData) {
        console.log(parsedData);
    });
```

The `then()` call is related to async/await in Javascript. `then()` is the logic to run after the `d3.csv()` finished.

```js
var dataset = await d3.csv("data/time_scale_data.csv", csvRowConverter)
    .then(function(parsedData) {
        console.log(parsedData);
        return parsedData;
    });
```

<img src="images/time_scale_data_as_objects.png" width="700">

#### Scaling time

We use `d3.timeScale` to create a scale that maps `Date` objects to pixel values in our SVG scatterplot:
```js
var xScale = d3.scaleTime()
    .domain([
        d3.min(dataset, function(data) { return data.date; }),
        d3.max(dataset, function(data) { return data.date; })
    ])
    .range([
        padding, svgWidth - padding
    ]);
```

Use the amount value to scale the circle's size:
```js
var areaScale = d3.scaleSqrt()
    .domain([0, d3.max(dataset, function(data) { return data.amount; })])
    .range([0, 10]); // Range is arbitrary - what matters is that the circle areas are relative
```

Create labels for each circle/data point (see [locale format](https://d3js.org/d3-time-format#locale_format) for formatting options):
```js
// Display date/amount value for each circle
var formatTime = d3.timeFormat("%b %e");
svg.selectAll("text")
    .data(dataset)
    .enter()
    .append("text")
    .text(function(data, index) { return formatTime(data.date); })
    .attr("x", function(data, index) { return xScale(data.date); })
    .attr("y", function(data, index) {
        return yScale(data.amount) + 25; // padding
    })
    .attr("text-anchor", "middle")
    .attr("font-size", "14")
    .attr("fill", "#736272")
    .attr("font-family", "sans-serif");
```

<img src="images/scatterplot-amount-labels.png" width="600">

<img src="images/scatterplot-time-labels.png" width="600">

<img src="images/scatterplot-day-label.png" width="600">

## Chapter 8: Axes

[D3 axes](https://d3js.org/d3-axis) are *functions* that generate the visual elements of an axis, like ticks, values, labels.
* D3 axes generates visuals, D3 scales return values.
* Axes are tied to SVGs, and only work with numerical values (as opposed to categorical).


### Setting up an axis

Four axis constructors:
* `d3.axisTop`
* `d3.axisBottom`
* `d3.axisRight`
* `d3.axisLeft`

In our scatter plot, we can create an axis:

```js
var xAxis = d3.axisBottom(xScale);
svg.append("g") // SVG group
    .attr("class", "axis") // Name the SVG group to axis"
    .call(xAxis); // Passes the `g` SVG element to xAxis()
```

The [`g` SVG element](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Element/g) means group.

<img src="images/axis-dates.png" width="600">

### Positioning Axis

We can apply an [SVG transform](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Attribute/transform) to `axisBottom()` to bring the axis to the bottom. Possible transforms: `translate`, `scale`, `rotate`, `skew`.

```js
svg.append("g") // SVG group
    .attr("class", "axis")  // Name the SVG group to axis"
    .attr("transform", "translate(0, " + (svgHeight - padding) + ")")
    .call(xAxis); // Passes the `g` SVG element to xAxis()
```


<img src="images/axis-bottom.png" width="600">

CSS styles can be applied to the axis using the `.axis` selector and modifying `path`, `line`, and `text`:

[SVG `shape-rendering` property](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Attribute/shape-rendering) for crisper edges, optimize for speed, etc.

Note that CSS property names can differ from SVG attribute names (ex. CSS `color` vs. SVG`fill`). See [SVG attributes](https://developer.mozilla.org/en-US/docs/Web/SVG/Reference/Attribute).


### Check for Ticks
(笑)

We can adjust the number of ticks on the axis (see [docs](https://d3js.org/d3-array/ticks)).

```js

var xAxis = d3.axisBottom(xScale).ticks(5);
```

<img src="images/axis-5-ticks.png" width="600">

We specified 5 ticks but D3 tries to add the optimal amount of ticks for the axis to be easily readable.

Manual tick values can also be used with `axisBottom().tickValues([])` (but does not work for time scales...).

### Y Not?

```js
var yAxis = d3.axisLeft(yScale);
svg.append("g")
    .attr("class", "axis")
    .attr("transform", "translate(" + padding  + ", 0)")
    .call(yAxis);
```

<img src="images/y-axis.png" width="600">

### Final Touches

Create a random dataset on webpage refresh and generate a scatter plot:

```js
// Make a random dataset and plot a scatterplot
var dataset = [];
var xMax = 100;
var yMax = 100;
var numPoints = 50;
for (var i=0; i<numPoints; i++) {
    var x = Math.floor(Math.random() * xMax);
    var y = Math.floor(Math.random() * yMax);
    dataset.push([x, y]);
}

// Create scales for x and y values
var svgPadding = 50;
var xScale = d3.scaleLinear()
    .domain([
        d3.min(dataset, function(p) { return p[0]; }),
        d3.max(dataset, function(p) { return p[0]; })
    ]) //
    .range([svgPadding, svgWidth - svgPadding]); // which pixel in the SVG to map to

var yScale = d3.scaleLinear()
    .domain([
        d3.min(dataset, function(p) { return p[0]; }),
        d3.max(dataset, function(p) { return p[0]; })
    ])
    .range([svgHeight - svgPadding, svgPadding]);

var svg = d3.select("body")
    .append("svg")
    .attr("width", svgWidth)
    .attr("height", svgHeight)
    .attr("style", "outline: thin solid #dedede");

svg.selectAll("circle")
    .data(dataset)
    .enter()
    .append("circle")
    .attr("cx", function(data, index) {
        return xScale(data[0]);
    })
    .attr("cy", function(data, index) {
        return yScale(data[1]);
    })
    .attr("r", 3)
    .attr("fill", "#c46021"); 

var xAxis = d3.axisBottom(xScale);
svg.append("g") // SVG group
    .attr("class", "axis")  // Name the SVG group to axis"
    .attr("transform", "translate(0, " + (svgHeight - svgPadding + 15) + ")")
    .call(xAxis); // Passes the `g` SVG element to xAxis()

var yAxis = d3.axisLeft(yScale);
svg.append("g")
    .attr("class", "axis")
    .attr("transform", "translate(" + (svgPadding) + ", 15)")
    .call(yAxis);
```

<img src="images/random-scatterplot.png" width="600">

### Formatting Tick Labels

We can use [`axis.tickFormat`](https://d3js.org/d3-axis#axis_tickFormat) if we want cleaner labels on our axes when using decimals/percentages.

We define the formatting we want with `d3.format()`, for example:
```js
var formatAsPercentage = d3.format(".1%");
```

This returns a function, which can format any floating point to a percentage string.
```js
> var formatAsPercentage = d3.format(".1%");
> formatAsPercentage(0.1);
"10.0%" 
```

We pass this function to the axis `tickFormat()`:
```js
xAxis.tickFormat(formatAsPercentage);
```

### Time-Based Axes

Add some padding to the xScale/yScale, which adds a bit of padding on the axes:
```js
// Create new Date objects, otherwise a reference to the dataset is given
// (which then we modify the min/max Date values)
var minDate = new Date(d3.min(dataset, function(data) { return data.date; }));
minDate.setDate(minDate.getDate() - 1);
var maxDate = new Date(d3.max(dataset, function(data) { return data.date; }));
maxDate.setDate(maxDate.getDate() + 1) 
var xScale = d3.scaleTime()
    .domain([minDate, maxDate])
    .range([
        padding, svgWidth - padding
    ]);
// y-values of amounts
var yScale = d3.scaleLinear()
    .domain([
        d3.min(dataset, function(data) { return data.amount; }) - 10,   // padding
        d3.max(dataset, function(data) { return data.amount; }) + 10    // padding
    ])
    .range([
        svgHeight - padding, padding
    ]);
```

Format the x-axis:
```js
var timeFormat = d3.timeFormat("%b %d");
var xAxis = d3.axisBottom(xScale)
    .tickFormat(timeFormat)
    .ticks(5);
```

<img src="images/format-axes.png" width="600">
