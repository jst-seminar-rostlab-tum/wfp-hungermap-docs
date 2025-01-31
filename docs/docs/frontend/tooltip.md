# Tooltip

**Author:** `Linus Sander`

The Tooltip component renders a customizable tooltip. It is based on the NextUI Tooltip component.

It should be used as a wrapper for one or more components.  When hovering over these wrapped components, the tooltip appears with a delay.

## Parameters

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

![Tooltip example simple](/img/tooltip_example_simple.png)

``` html
<Tooltip title="Lorem ipsum" text="Consetetur sadipscing elitr, sed diam nonumy eirmod tempor.">
    <div className="p-2 border-1"> Dummy Box </div>
</Tooltip>
```

### Maxed out example

![Tooltip example maxed out](/img/tooltip_example_maxed_out.png)

``` html
<Tooltip
    title="Lorem ipsum"
    text="Consetetur sadipscing elitr, sed diam nonumy eirmod tempor."
    delay={100}
    warning
    titleStyle="italic"
    textStyle="text-gray-400"
    offset={40}
>
    <div className="p-2 border-1"> Dummy Box</div>
</Tooltip>
```
