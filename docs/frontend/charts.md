---
sidebar_position: 3
---

# Charts

Two components were created to enable the visualization of charts. One is tailored for categorical data, 
and the other for continuous data. Both provide various practical customization options, 
which will be explained in detail in the following sections.

Both components are boxes that primarily render a title, description text, and a chart.
For continuous data the `ContinuousChart` component and for categorical data the `CategoricalChart` should be used.
The components have a width of 100%, so they adjust to the width of their parent element in which they are used.
The height of the boxes depend on the provided text, while the chart itself has a fixed height.
They also provide the option to open the chart in a full-screen modal, where one can download the data as well.

todo explain additional functionality 
- slider
- chart switch
- download

## Parameters

The following section provides a detailed explanation of the individual parameters for the two chart components. 
For more information on the structure of the expected data object by `params.data, please refer to the 'Data' section below.

### `ContinuousChart` Parameters

- `data` ContinuousChartData: the actual data to be used in the chart
- `title` string: chart title (optional)
- `description` string: chart description text (optional)
- `small` boolean: when selected, all components in the main box become slightly smaller (optional)
- `noPadding` boolean: when selected, the main box has no padding on all sides (optional)
- `transparentBackground` boolean: when selected, the background of the entire component is transparent (optional)
- `chartHeight` number: with this parameter, the height of the actual chart can be set to a fixed value in pixels. If chartHeight is not specified, the chart is given a fixed default height depending on `small`.
- `disableExpandable` boolean: when selected, the functionality to open the chart in a larger modal is disabled (optional)
- `disableBarChartSwitch` boolean: when selected, the functionality to switch to a bar chart is disabled (optional)
- `disableXAxisSlider` boolean: when selected, the functionality to change the x-axis range via a slider is disabled (optional)
- `disableDownload` boolean: when selected, the functionality to download the chart is disabled (optional)

### `CategoricalChart` Parameters

- `data` CategoricalChartData: the actual data to be used in the chart
- `title` string: chart title (optional)
- `description`string: chart description text (optional)
- `small` boolean: when selected, all components in the line chart box become slightly smaller (optional)
- `noPadding` boolean: when selected, the main box has no padding on all sides (optional)
- `transparentBackground` boolean: when selected, the background of the entire component is transparent (optional)
- `chartHeight` number: with this parameter, the height of the actual chart can be set to a fixed value in pixels. If chartHeight is not specified, the chart is given a fixed default height depending on `small`.
- `disableExpandable` boolean: when selected, the functionality to open the chart in a larger modal is disabled (optional)
- `disablePieChartSwitch` boolean: when selected, the functionality to switch to a pie chart is disabled (optional)
- `disableDownload` boolean: when selected, the functionality to download the chart is disabled (optional)


## Data

This section provides a detailed description of the data expected by each of the two components for their respective `params.data` parameter.

### ContinuousChartData

The `ContinuousChart` component expects a data object of type `CategoricalChartData`. In this object, the `type` 
parameter is set to `ContinuousChartDataType.LINE_CHART_DATA` to inform the chart component that it is dealing with 
`LINE_CHART_DATA`. Additionally, the `xAxisType` parameter specifies the exact type of the x-axis. For more information
about the available types, see TODO. The optional `yAxisLabel` parameter can be used to set the title of the y-axis.

TODO predictionVerticalLineX

The actual data is stored in `lines`, which contains an array where each entry represents a data series ('line') in the chart. 
Each `lines` element must have a `name` and an array called `dataPoints`, which includes all data points of this data series. 
For each individual data point, an `x` value must be specified, and a `y` value can be optionally provided. 
If a range around an individual data point should also be displayed in the chart, this can be defined using `yRangeMin`
and `yRangeMax` for each individual data point.

todo continue

``` javascript
export interface ContinuousChartDataPoint {
  x: number;
  y?: number;
  yRangeMin?: number;
  yRangeMax?: number;
}

export interface ChartVerticalLine {
  x: number;
  color?: string;
  dashStyle?: DashStyleValue;
}

export interface ChartVerticalBand {
  xStart?: number;
  xEnd?: number;
  color?: string;
  label?: string;
}

export interface ContinuousChartData {
  type: ContinuousChartDataType.LINE_CHART_DATA;
  xAxisType: AxisTypeValue;
  yAxisLabel?: string;
  predictionVerticalLineX?: number;
  lines: {
    name: string;
    dataPoints: ContinuousChartDataPoint[];
    showRange?: boolean;
    color?: string;
    dashStyle?: DashStyleValue;
    prediction?: boolean;
  }[];
  verticalLines?: ChartVerticalLine[];
  verticalBands?: ChartVerticalBand[];
}
```


### CategoricalChartData

The `CategoricalChart` component expects a data object of type `CategoricalChartData`. In this object, 
the title of the y-axis label can be defined (optional), along with an arbitrarily long `categories` array 
where each entry corresponds to a category. A category consists of a `name`, a `dataPoint` containing only the 
actual data point (`y`), and an optional `color` attribute.

``` javascript
export interface CategoricalChartData {
  yAxisLabel?: string;
  categories: {
    name: string;
    dataPoint: {
      y: number;
    };
    color?: string;
  }[];
}
```

- `children` React.ReactNode: the wrapped React components
- `title` string: title of the tooltip (optional)
- `text` string: textual content of the tooltip
- `delay` number: delay with which tooltip appears on hover in milliseconds; default is 0 (optional)
- `warning` boolean: selected if the tooltip should be highlighted; border of the tooltip is coloured in a warning color (optional)
- `titleStyle` string: additional tailwind classes to style the title (optional)
- `textStyle` string: additional tailwind classes to style the text (optional)
- `offset` number: offset of the tooltip, default is set to 10 (optional)

## Examples

### Simple example

todo

### Maxed out example

todo
