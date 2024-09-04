# Calculations

Calculation files define custom MDX expressions for creating calculated
metrics in AtScale. They can be used to combine, evaluate, or manipulate
other metrics defined in the model. For example, you can do simple math
operations to combine metrics, or simple comparison operations to return
a given metric value when certain conditions are met.

In SML, calculation files are a subset of metrics. The separation of
calculation metrics from other types enables you to easily create
boilerplate calculations that can be used across multiple metrics.

Sample `calculation` file:

```
unique_name: Average Catalog Unit Net Profit
object_type: metric_calc
label: Average Catalog Unit Net Profit
expression: "[Measures].[m_cs_net_profit_sum]/[Measures].[m_cs_quantity_sum]"
```

# Calculation Properties

## unique_name

- **Type:** string
- **Required:** Y

The unique name of the calculation. This must be unique across all
repositories and subrepositories.

## object_type

- **Type:** const
- **Required:** Y

The type of object defined by the file. For calculations, the value of
this property must be `metric_calc`.

## label

- **Type:** string
- **Required:** Y

The name of the calculation as it appears in AtScale. This value does
not need to be unique.

## expression

- **Type:** string
- **Required:** Y

The MDX expression to use for the calculation.

This expression must be written in MDX syntax, surrounded by quotes (").
Additionally, it can only operate on existing metrics in the model, and
must return a numeric value.

:::note
*Note:* AtScale only supports a small subset of MDX functions and
operators. For more information, see [MDX
Reference](../../c-creating-and-sharing-cubes/creating-cubes/modeling-cube-measures/add-calculated-measures/mdx-reference/index.md).
:::

## description

- **Type:** string
- **Required:** N

A description of the calculation.

## format

- **Type:** string
- **Required:** N

The format of the values returned by the calculation. You can use one of
AtScale's built-in named formats, or a custom string format.

Supported named formats:

- `fixed`
- `general number`
- `none`
- `percent`
- `scientific`
- `standard`

Custom format strings should be in quotes (") and contain one to four
sections, separated by semicolons. For more information on defining
custom format strings, see [Number Format
Strings](../../c-creating-and-sharing-cubes/creating-cubes/formats-for-data-values/number-format-strings.md).

## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the calculation is visible in BI tools.

Supported values:

- `true`
- `false`

## mdx_aggregation_function

- **Type:** string
- **Required:** N

The aggregation function to use when this calculation is referenced via
the [Aggregate MDX
function](../../c-creating-and-sharing-cubes/creating-cubes/modeling-cube-measures/add-calculated-measures/mdx-reference/aggregate.md).

:::note
*Note:* AtScale recommends including this property if you plan on
referencing the calculation from calculation groups. Otherwise, you may
encounter errors at query time. For more information on calculation
groups, see [Using Calculation
Groups](../../c-creating-and-sharing-cubes/creating-cubes/modeling-cube-dimensions/using-calculation-groups/index.md)
and
[Dimensions](../../c-creating-and-sharing-cubes/sml-reference/dimensions.md#calculation-groups).
:::

Supported values:

- `SUM`
- `MAX`
- `MIN`
- `COUNT`
- `AVG`
