# ng3-charts

![](https://raw.githubusercontent.com/n3-charts/line-chart/gh-pages/assets/images/n3-charts.png)

ng3-charts is a fork from 'n3-charts.linechart' with a couple additions.  It gives you the ability to create beautiful charts for [AngularJS](http://angularjs.org/) applications in an easy and semantic way. It is built on top of [D3.js](http://d3js.org/).

You can find examples on the [demo page](http://n3-charts.github.io/line-chart/).

### How to install
 + Install using bower : `bower install ng3-charts` (or copy `ng3-charts.js` wherever you want)
 + Reference `ng3-charts.js` in your index.html file
 + Reference the module in your app file :
    ```
    angular.module('myApp', ['ng3-charts'])
    ```

### How to use
A line chart is called using this syntax :

```html
<linechart data="data" options="options" mode="" width="" height=""></linechart>
```

The line chart directives needs two attributes : `data` and `options`. If one is missing, nothing happens.

#### Data
Your data should look like this :

```js
$scope.data = [
  {x: 0, value: 4, otherValue: 14},
  {x: 1, value: 8, otherValue: 1},
  {x: 2, value: 15, otherValue: 11},
  {x: 3, value: 16, otherValue: 147},
  {x: 4, value: 23, otherValue: 87},
  {x: 5, value: 42, otherValue: 45}
];
```

#### Options
Options must be an object with a series array. It should look like this :

```js
$scope.options = {
  axes: {
    x: {key: 'x', labelFunction: function(value) {return value;}, type: 'linear', min: 0, max: 10, ticks: 2},
    y: {type: 'linear', min: 0, max: 1, ticks: 5},
    y2: {type: 'linear', min: 0, max: 1, ticks: [1, 2, 3, 4]}
  },
  series: [
    {y: 'value', color: 'steelblue', thickness: '2px', type: 'area', striped: true, label: 'Pouet'},
    {y: 'otherValue', axis: 'y2', color: 'lightsteelblue', visible: false, drawDots: true, dotSize: 2}
  ],
  lineMode: 'linear',
  transition: {ease: 'elastic', duration: 1000, delay: 50},
  tension: 0.7,
  tooltip: {mode: 'scrubber', formatter: function(x, y, series) {return 'pouet';}},
  drawLegend: true,
  drawDots: true,
  columnsHGap: 5
}
```
##### Axes
The `axes` keys can be undefined. Otherwise, it can contain an `x̀` key with the following properties :

 + `key` : optional, defines where the chart will look for abscissas values in the data (default is 'x').
 + `type` : optional, can be either 'date' or 'linear' (default is 'linear'). If set to 'date', the chart will expect Date objects as abscissas. No transformation is done by the chart itself, so the behavior is basically D3.js' time scale's.
 + `labelFunction` : optional, allows to format the axis' ticklabels. Must be a function that accepts a single argument and returns a string.
 + `min` : optional, forces the axis minimum value (default is computed from data)
 + `max` : optional, forces the axis maximum value (default is computed from data)
 + `ticks` : optional, configures the axis' ticks (can be either a number or an array, more on this [here][3])

It can also contain, according to your series configuration, a `y` and a `y2` key with the following properties :

 + `type` : optional, can be either linear' or 'log' (default is 'linear'). If set to 'log', the data may be clamped if its computed lower bound is 0 (this means the chart won't display an actual 0, but a close value - log scales can't display zero values).
 + `min` : optional, forces the axis minimum value (default is computed from data)
 + `max` : optional, forces the axis maximum value (default is computed from data)


##### Series
The `series` key must be an array which contains objects with the following properties :

+ `y` : mandatory, defines which property on each data row will be used as ordinate value.
+ `stacks`: an array that allows you to specify which datum you would like to stack on top of eachother.  More info with example below.
+ `color` : optional, any valid HTML color (if none given, the chart will set it for you).
+ `label` : optional, will be used in the legend (if undefined, the `y` value will be used).
+ `axis` : optional, can be either 'y' (default, for left) or 'y2' (for right). Defines which vertical axis should be used for this series. If no right axis is needed, none will be displayed.
+ `type` : optional, can be one of the following values 'line', 'area', 'column', and 'rangearea'. Default is 'line'.  Further information on types can be found below.
+ `striped` : optional, can be either `true` or `false`. Default is `false`. Will be ignored if the series type is not 'area'.
+ `thickness` : optional, can be `{n}px`. Default is `1px`. Will be ignored if the series type is not 'area' or 'line'.
+ `lineMode` : optional, can be `dashed`. Default is undefined. Defines whether the series is rendered as a dashed line. Removed if the series type is not `line` or `area`.
+ `drawDots` : optional, can be either `true` or `false`. Default is true. Defines whether the series is rendered with dots on a per `series` basis. Overrides the global setting.
+ `visible` : optional, can be either `true` or `false`. Default is true. Defines whether the series is initially visible. Will be updated if the series gets hidden or shown through a click on the legend.
+ `dotSize` : optional, must be an numerical value. Default is `2`. Will be ignored if the series type is not `area` or `line`, or if `drawDots` is set to `false`.

#####Chart Types
Within the series section for the chart you can specify 4 different chart types ('line', 'area', 'column', and 'rangearea').  The default is a line chart type.  
######Line
The line chart is just that.  A line that maps to your data.

######Area
An area chart is just like a line chart, however, the area below the line is filled in given a specific color.

######Column
Column charts are just as the name describes a way to draw a column or bar chart.

######RangeArea
Range area is just like the area chart, however, in an area chart the colors will overlap each other causing the colors to lose their initial value.  With a rangearea chart the colors are persistent and will not overlap.  Sort of like a stream graph.  One thing about rangearea charts is that you cannot use the datum in a stacked option. 

![](http://res.cloudinary.com/buddahbelly/image/upload/v1417529675/rangearea.png)


####Stacks
Stacks allow you to stack your data in a chart. For example, say I would like to stack a column chart with a couple of `series` that I listed in my `series` section like so:

 ```js
 series: [
{id: 'ssIncome', y: 'ssIncome', axis: 'y', color: getColors().ssIncome, thickness: '2px', type: 'column', label: 'Social Security'},
{id: 'ptIncome', y: 'ptIncome', axis: 'y', color: getColors().ptIncome, type: 'column', drawDots: true, dotSize: 4, label: 'Part Time'},
{id: 'invIncome', y: 'invIncome', axis: 'y', color: getColors().invIncome, type: 'column', dotSize: 2, label: 'Investments'}]
```
With my series defined I can now add them to a stack using their id defined in the series:
```javascript
     stacks: [{axis: "y", series: ['data1', 'data2', 'data3']}]
```
This will stack the data one on top of each other depending on their order, so for this example data1 will go from 0 to its upper limit, data2 will go from data1's upper limit to it's own upper limit, and data3 will go from data2's upper limit to it's own upper limit. 

There is something that is noteworthy about stacked graphs and that is that they add their numbers together, so you will get the sum of the data when stacked like the image below:

![](http://res.cloudinary.com/buddahbelly/image/upload/v1417529580/ng3-charts/Screen_Shot_2014-12-02_at_9_12_38_AM.png)

#####Transition
The `transition` option must be an object which contains the following properties:

 - ease : this allows you to specify which type of easing you want to use (ie - linear, elastic)
 - duration : allows you to control how long you want the transition to last for  in milliseconds
 - delay : amount of delay before starting transition.  This defaults to zero

As for right now this transitions only works for drawing column/bar charts.  There will be support for other types in the near future.

Usage:

```js
         transition: {ease: 'elastic', duration: 1000, delay: 50},
```
NOTE: These options correspond to the d3 docs on transitions found [d3 transitions](https://github.com/mbostock/d3/wiki/Transitions)

##### Tooltip
The `tooltip` must be an object which contains the following properties :
 + `mode` : can be set to `none`, `axes`, or `scrubber`. It can also be set to `scrubber`, which displays tooltips for all series. Default is `scrubber`.
 + `interpolate` : can be either `true`or `false`. Default is `false`. Will be ignored if the tooltip's mode is not `axes`.
 + `formatter` : optional, allows to catch the tooltip before it gets rendered. Must be a function that takes `x`, `y` and `series` as arguments and returns a string. Ignored when mode is not `scrubber`.

##### Optional stuff
Additionally, you can set `lineMode` to a value between these :

+ linear *(default)*
+ step-before
+ step-after
+ basis
+ basis-open
+ basis-closed
+ bundle
+ cardinal
+ cardinal-open
+ cadinal-closed
+ monotone

The `tension` can be set, too (default is `0.7`). See [issue #44][2] about that.

> For more information about interpolation, please consult the [D3.js documentation about that][1].

The `drawLegend` and `drawDots` are optional. They respectively enable/disable the chart's legend and the lines and areas dots. Default is `true` for both.

The `columnsHGap` is optional (default is `5`). Sets the space between two columns. If you haven't any column series on your chart but are wondering why this option doesn't do anything, please don't send me an email.

#### Mode
The mode can be set to 'thumbnail' (default is empty string). If so, the chart will take as much space as it can, and it will only display the series. No axes, no legend, no tooltips. Furthermore, the lines or areas will be drawn without dots. This is convenient for sparklines.

#### Width and height
This is more a hack. The chart usually tries to infer its own dimensions regarding its parent, but sometimes it fails like a noob. That's why these two attributes can be set. The should look like this :

```html
<linechart width="150" height="100"></linechart>
```

### Building
Fetch the repo :
```sh
$ git clone https://github.com/n3-charts/line-chart.git
```

Install dev dependencies :
```sh
$ npm install
```

Install components :
```sh
$ bower install
```

###Grunt
I am currently working on getting a test suite as well as other grunt tasks to build a minified version and watch files.


### Testing
Currently there are no tests for this project.  I am currently working to get the pre existing tests from line-chart.js to work in this project

  [1]: https://github.com/mbostock/d3/wiki/SVG-Shapes#wiki-line_interpolate
  [2]: https://github.com/n3-charts/line-chart/issues/44
  [3]: http://stackoverflow.com/a/11661725



> Written with [StackEdit](https://stackedit.io/).
