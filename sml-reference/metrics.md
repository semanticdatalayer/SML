# Metrics

Metric files define metrics to be used in your repository. A metric is a
numeric value representing a summarized (or aggregated) dataset metric,
such as the sum of sales or average order quantity. Metrics always
result from an aggregate calculation applied to one or more columns of a
fact dataset.

AtScale supports the following types of metrics:

- **Additive:** Metrics whose values can be summarized for any dimension
  attribute of the model and then combined consistently.
- **Non-additive:** Metrics whose values cannot be summed across any
  dimensional groupings using basic addition, since this would typically
  produce an inaccurate result. The most common example of a
  non-additive metric is a distinct count of an attribute value.
- **Semi-additive:** Metrics whose values can be summarized for some
  dimensions in a model, but not all. Ratios such as average are also
  considered semi-additive metrics.

:::note
**Note:** AtScale also supports calculated metrics, which in SML are
defined in calculation files. For more information, see
[Calculations](%7B%7BcurrentVersion%7D%7D/c-creating-and-sharing-cubes/sml-reference/calculations).
:::

For more information on how metrics are used within AtScale, see
[Modeling
Metrics](../../c-creating-and-sharing-cubes/creating-cubes/modeling-cube-measures/index.md).

Sample `metric` file:

```
unique_name: m_catalog_sales_coupon_amount_avg
object_type: metric
label: Catalog Sales  Average Coupon Amount
calculation_method: average
dataset: catalog_sales
column: cs_sales_price
```

Metric files support the following properties.

## unique_name

- **Type:** string
- **Required** Y

The unique name of the metric. This must be unique across all
repositories and subrepositories.

## object_type

- **Type:** const
- **Required** Y

The type of object defined by the file. For metrics, the value of this
property must be `metric`.

## label

- **Type:** string
- **Required** Y

The name of the metric, as it appears in AtScale. This value does not
need to be unique.

## calculation_method

- **Type:** string
- **Required** Y

The method used to aggregate query results for the metric.

Supported values:

- `average`
- `count distinct`
- `count non-null`
- `estimated count distinct`
- `maximum`
- `minimum`
- `percentile`
- `stddev_pop`
- `stddev_samp`
- `sum`
- `var_pop`
- `var_samp`

The calculation method you can use depends on the type of metric you're
creating:

- **Semi-additive:** `average`, `sum`, `minimum`, `maximum`
- **Non-additive:** `count distinct`, `percentile`
- **Additive:** All other options

## dataset

- **Type:** string
- **Required:** Y

The source dataset that contains the column the metric is based on.

## column

- **Type:** column
- **Required:** Y

The specific column within the `dataset` that the metric is based on.

## description

- **Type:** string
- **Required** N

A description of the metric.

## semi_additive

- **Type:** object
- **Required** N

Defines the metric as a semi-additive metric.

`semi_additive` supports the following properties.

### position

- **Type:** string
- **Required:** Y

Determines whether the metric is First Non-Empty or Last Non-Empty.

Supported values:
- `first`
- `last`

### dimension

- **Type:** string
- **Required:** Y

The dimension with which the semi-additive metric is associated.

### hierarchy

- **Type:** string
- **Required:** Y

The hierarchy with which the semi-additive metric is associated.

### level

- **Type:** string
- **Required:** Y

The level with which the semi-additive metric is associated.

## compression

- **Type:** number
- **Required:** N

Only for non-additive metrics using a `calulation_method` of
`percentile`. Defines the compression score the AtScale engine uses when
estimating percentile values for query results.

You can specify a value 1 - 50,000.

Using a higher compression score yields more accurate query results but
requires more memory from the engine to process. You may need to run
tests to determine the right level of compression for your needs.

For more information, see [Non-Additive
Metrics](../../c-creating-and-sharing-cubes/creating-cubes/modeling-cube-measures/types-of-cube-measures/non-additive-measures.md).

:::note
*Note:* In Design Center, compression is referred to as **Quality**.
:::

## named_quantiles

- **Type:** string
- **Required:** Required if `calculation_method` is `percentile`

Only for non-additive metrics using a `calulation_method` of
`percentile`. Defines the quantile to use for query results.

Supported values:

- `quartiles`
- `median`
- `deciles`

## format

- **Type:** string
- **Required:** N

The format in which query results are returned. You can use one of
AtScale's built-in named formats or a custom format string.

Supported named formats:

- `fixed`
- `general number`
- `none`
- `percent`
- `scientific`
- `standard`

Custom format strings should be in quotes and contain one to four
sections, separated by semicolons. For more information on defining
custom format strings, see [Number Format
Strings](../../c-creating-and-sharing-cubes/creating-cubes/formats-for-data-values/number-format-strings.md).

## unrelated_dimensions_handling

- **Type:** string
- **Required:** N

Determines how the AtScale engine behaves when all of the following
conditions are true:

- A client queries a model that contains multiple fact datasets.
- The data in each fact dataset are at a different level of granularity
  than the data in the other fact datasets.
- The query references dimensions that are not related to the metrics
  being queried.

Supported values:

- `error`: AtScale rejects the query and returns an error message.
- `empty`: AtScale displays empty cells in the query results.
- `repeat`: In the query results, AtScale repeats the values for the
  metric at a level of aggregation that is determined from the shared
  dimensions in the query.

## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the metric is visible in BI tools.
