# Dimensions

Dimension files define the dimensions used in the model. A *dimension*
is a logical collection of attributes that are bound to specific columns
in a source dataset. These attributes are in turn used to group and
filter metric data at query time.

SML supports the following types of dimensions:

- **Normal:** Dimensions that are based on a dataset. All data for a
  normal dimension is normalized into a single table or view. There are
  two types of normal dimensions:
- **Standard:** Can have any type of hierarchy.
- **Time:** Must have a time hierarchy.

- **Degenerate:** A dimension that is based on one or more columns in a
  fact dataset.

- **Shared degenerate:** A dimension that is based on one or more
  columns that are common to two or more fact datasets.

- **Snowflake:** A logical dimension that is composed of multiple
  underlying physical datasets.

- **Many-to-many:** Also called multi-valued. This is when a fact
  dataset row refers to more than one row in a dimension dataset. In
  SML, this is modeled by defining a dimensional bridge or junction
  table to resolve the many-to-many relationship.

Sample `dimension` file:

```
unique_name: Store Dimension
object_type: dimension
label: Store Dimension
type: standard

hierarchies:

  - unique_name: Store Dimension
    label: Store Dimension
    folder: Store Attributes
    filter_empty: "yes"

    levels:

      - unique_name: d_store_country

      - unique_name: d_store_state

      - unique_name: d_store_county

      - unique_name: d_store_city

      - unique_name: Store Dimension

        secondary_attributes:

          - unique_name: d_s_floor_space
            label: Store Floor Space
            folder: Store Attributes
            dataset: store
            name_column: s_floor_space
            key_columns:
              - s_floor_space
            sort_column: s_floor_space

          - unique_name: d_s_manager
            label: Store Manager
            folder: Store Attributes
            dataset: store
            name_column: s_manager
            key_columns:
              - s_manager
            sort_column: s_manager

          - unique_name: d_s_number_employees
            label: Store Number of Employees
            folder: Store Attributes
            dataset: store
            name_column: s_number_employees
            key_columns:
              - s_number_employees
            sort_column: s_number_employees

          - unique_name: d_store_company_id
            label: Store Company ID
            folder: Store Attributes
            dataset: store
            name_column: s_company_id
            key_columns:
              - s_company_id
            sort_column: s_company_id

          - unique_name: d_store_name
            label: Store Name
            folder: Store Attributes
            dataset: store
            name_column: s_store_name
            key_columns:
              - s_store_name
            sort_column: s_store_name

level_attributes:

  - unique_name: d_store_city
    label: Store City
    dataset: store
    name_column: s_city
    key_columns:
      - s_country
      - s_state
      - s_city

  - unique_name: d_store_country
    label: Store Country
    dataset: store
    name_column: s_country
    key_columns:
      - s_country

  - unique_name: d_store_county
    label: Store County
    dataset: store
    name_column: s_county
    key_columns:
      - s_state
      - s_county

  - unique_name: d_store_state
    label: Store State
    dataset: store
    name_column: s_state
    key_columns:
      - s_country
      - s_state

  - unique_name: Store Dimension
    label: Store Number
    is_unique_key: true
    dataset: store
    name_column: s_store_sk
    key_columns:
      - s_store_sk
```

# Entitity Relationships

```mermaid
classDiagram
    Relationship *-- From
    Relationship *-- To
    Dimension *-- Relationship
    Dimension *-- Hierarchy
    Dimension *-- LevelAttribute
    Dimension *-- CalculationGroup
    Hierarchy *-- Level
    LevelAttribute *-- CustomEmptyMember
    Level *-- SecondaryAttribute
    Level *-- MetricalAttribute
    Level *-- Alias
    CalculationGroup *-- CalculatedMembers
    SecondaryAttribute *-- CustomEmptyMember
    Alias *-- CustomEmptyMember
    MetricalAttribute *-- CustomEmptyMember    
namespace Dimensions{
    class Dimension{
      String unique_name
      String label
      const object_type
      String description
      enum type
      Boolean is_degenerate
      Array~Hierarchy~ hierarchies
      Array~LevelAttribute~ level_attributes
      Array~Relationship~ relationships
      Array~CalculationGroup~ calculation_groups
    }
    class Relationship{
      String unique_name
      Object from
      Object to
      String role_play
      String type
      Boolean m2m
    }
    class From{
      String dataset
      Array~Column~ columns
    }
    class To{
      String dimension
      String level
      String row_security
    }
    class Hierarchy{
      String unique_name
      String label
      String folder
      enum filter_empty
      Array~Level~ levels
    }
    class Level{
      String unique_name
      String description
      Array~SecondaryAttribute~ secondary_attributes
      Array~Alias~ aliases
      Array~MetricalAttribute~ metrics
      String default_member
      Boolean is_hidden
    }
    class Alias{
      String unique_name
      String label
      String description
      String dataset
      String name_column
      String sort_column
      String folder
      Boolean is_hidden
      Boolean exclude_from_dim_agg
      Boolean exclude_from_fact_agg
      Array~CustomEmptyMember~ custom_empty_member
      Array~String~ allowed_calcs_for_dma
      Object role
    }
    class MetricalAttribute{
      String unique_name
      String label
      String description
      String folder
      String format
      String calculation_method
      String dataset
      String column
      Boolean is_hidden
      Boolean exclude_from_dim_agg
      Boolean exclude_from_fact_agg
      CustomEmptyMember custom_empty_member
      enum unrelated_dimensions_handling
      Array~String~ allowed_calcs_for_dma
    }
    class LevelAttribute{
      String unique_name
      String label
      String description
      String dataset
      String name_column
      String sort_column
      Array~String~ key_columns
      Boolean contains_unique_names
      Boolean is_hidden
      Boolean is_unique_key
      Boolean exclude_from_dim_agg
      Boolean exclude_from_fact_agg
      String time_unit
      Array~String~ allowed_calcs_for_dma
    }
    class SecondaryAttribute{
      String unique_name
      String label
      String description
      String folder
      String dataset
      String name_column
      String sort_column
      Array~String~ key_columns
      Boolean exclude_from_dim_agg
      Boolean exclude_from_fact_agg
      Array~String~ allowed_calcs_for_dma
      Array~CustomEmptyMember~ custom_empty_member
    }
    class CustomEmptyMember{
      Array~String~ key
      String name
      String sort_name
    }
    class CalculationGroup{
      String unique_name
      String label
      String description
      String folder
      Array~CalculatedMembers~ calculated_members
    }
    class CalculatedMembers{
      String unique_name
      String description
      String format
      String expression
      Boolean is_default
      Boolean use_input_measure_format
    }
}
```

# Dimension Properties

## unique_name

- **Type:** string
- **Required:** Y

The unique name of the dimension. This must be unique across all
repositories and subrepositories.

## object_type

- **Type:** const
- **Required:** Y

The type of object defined by the file. For dimensions, this value must
be `dimension`.

## label

- **Type:** string
- **Required:** Y

The name of the dimension, as it appears in the consunmption tool. This value does not
need to be unique.

## description

- **Type:** string
- **Required:** N

A description of the dimension.

## folder

- **Type:** string
- **Required:** N

The name of the folder in which the calculation group appears in BI
tools.

## type

- **Type:** enum
- **Required:** N

The type of dimension defined by this file.

Supported values:

- `standard`: Can have any type of hierarchy.
- `time`: Must have a time hierarchy.

## hierarchies

- **Type:** array
- **Required:** Y

Defines the hierarchies within the dimension.

Hierarchies organize the dimension attributes into categories or levels,
where each level is a subdivision of the level above. Every logical
dimension you create has at least one hierarchy with at least one level.

## level_attributes

- **Type:** array
- **Required:** Y

Level attributes are attributes associated with a particular dimension
hierarchy. Every hierarchy has a key level attribute, which is the most
granular representation of the dimension's data. Only level attributes
can be used to define relationships between datasets and other
dimensions.

## relationships

- **Type:** array
- **Required:** N

The `relationships` property in a dimension file defines the
relationships to embedded and snowflake dimensions.

:::note
*Note:* The relationships between the model's fact datasets and first
order dimensions (fact relationships) are defined in model files.
:::

## calculation_groups

- **Type:** array
- **Required:** N

The `calculation_groups` property in a dimension file defines
calculation groups to use in the dimension.

Dimension calculation groups offer a simplifying alternative to
calculated metrics by enabling the expression of boiler-plate
calculations across multiple metrics. This feature defines calculations
as dimension members and removes static references to individual
measures.

# Relationship Properties

## unique_name

- **Type:** string
- **Required:** N

The unique name of the relationship. This must be unique within the
dimension.

## from

- **Type:** object
- **Required:** Y

Defines the side of the relationship that contains the physical dataset
that you want to connect to another dimension.

Supported properties:

- `dataset`: String, required. The physical dataset you want to link to
  a dimension.
- `join_columns`: Array, required. The column(s) within the `dataset`
  that you want to use for the join.
- `hierarchy`: String, optional. The hierarchy within the dimension from
  which the relationship should originate.
- `level`: String, optional. The level within the `hierarchy` from which
  the relationship should originate.

For snowflake relationships (as defined by the `type` property), you
only need to define `dataset` and `join_columns`.

## to

- **Type:** object
- **Required:** Y

Defines the dimension that the `from` dataset is linked to.

Supported properties:

- `dimension`: String. The name of the dimension the `from` dataset is
  linked to.
- `level`: String, required if `row_security` is undefined. The key
  level within the dimension to use for the relationship.
- `row_security`: String, required if `level` is undefined. For security
  relationships, the [row
  security](../../c-creating-and-sharing-cubes/sml-reference/row-security.md)
  object that the `from` dataset is linked to.

For snowflake relationships (as defined by the `type` property), you
only need to define `level`.

## type

- **Type:** string
- **Required:** Y

Defines the relationship as either embedded or snowflake.

Supported values:

- `embedded`: A secondary relationship, or one that connects a primary
  dimension to a secondary dimension.
- `snowflake`: A relationship that connects one of several underlying
  physical datasets together to create a snowflake dimension.

## role_play

- **Type:** string
- **Required:** N

For role-playing relationships only. Defines the role-playing template
for the relationship.

The role-playing template is the prefix or suffix that is added to every
attribute in the role-played dimension. You can also specify both a
prefix and a suffix.

This value must be in one of the following formats (including quotation
marks):

- **Prefix:** `"<prefix> {0}"`
- **Suffix:** `"{0} <suffix>"`
- **Prefix and suffix:** `"<prefix> {0} <suffix>"`

For example, if you wanted to use the prefix **Order**, you would set
`role_play` to `"Order {0}"`.

# Calculation Group Properties

## unique_name

- **Type:** string
- **Required:** Y

The name of the calculation group. This must be unique within the
dimension.

## description

- **Type:** string
- **Required:** Y

A description of the calculation group.

## calculated_members

- **Type:** array
- **Required:** Y

Defines the individual calculations in the group.

# Calculated Members Properties

## unique_name

- **Type:** string
- **Required:** Y
 
The name of the calculation. This must be unique within the dimension.

## description

- **Type:** string
- **Required:** Y

A description of the calculation.

## format

- **Type:** String
- **Required:** N

The format in which query results are returned. You can use one of SML's built-in named formats or a custom format string. 

Supported named formats: 

`fixed`,`general number`, `none`, `percent`, `scientific`, `standard` 

Custom format strings should be in quotes and contain one to four sections, separated by semicolons. For more information on defining custom format strings.

## expression

- **Type:** string
- **Required:** Y

The MDX expression for the calculation. This must be in quotes. If you plan on referencing a calculated metric via the `Aggregate` MDX function in your calculation, ensure that the computed metric has an aggregation function set. You can do this by including the `mdx_aggregation_function` property in the calculation file. If you do not set an aggregation function, you may encounter errors at query time.

# Hierarchy Properties

## unique_name

- **Type:** string
- **Required:** Y

The unique name of the hierarchy. This must be unique within the dimension.

## label

- **Type:** string
- **Required:** Y

The name of the hierarchy. This value does not need to be unique.

## description

- **Type:** string
- **Required:** N

A description of the hierarchy.

## folder

- **Type:** string
- **Required:** N

The name of the folder in which to display this hierarchy in BI tools. If your model has a lot of dimensional hierarchies, folders are a good way to organize them.

## filter_empty

- **Type:** string
- **Required:** N

Configures the join behavior for the hierarchy, which determines how empty values are handled in client BI tools. The value you specify must be in quotes. 
  
Supported values:

- `yes`: Query results in BI tools only include members that join to
  the fact dataset (inner join behavior). Members with no matching
  entries in the fact dataset are still included if the client BI
  tool requests them.

- `no`: Query results include all members of the dimension, even
  those that have no matching entries in the fact dataset (outer
  join behavior). This occurs unless the client BI tool specifically
  requests to have these values filtered out.

- `always`: Query results only include members that join to the fact
  dataset (inner join behavior). This typically provides the best
  performance.

## default_member

- **Type:** string
- **Required:** N

Defines a member of the hierarchy to use as the default filter for MDX queries on the hierarchy. The value must be formatted as an MDX expression and must be in quotes.

## levels

- **Type:** array
- **Required:** Y

Defines the levels within the hierarchy. You can include as many levels as needed in the list.
  
# Level Properties

## unique_name

- **Type:** string
- **Required:** Y

Specifies the unique name of the level. This must be unique within the dimension.

## secondary_attributes

- **Type:** array
- **Required:** N

Defines secondary attributes for the dimension level. Secondary attributes are dimensional attributes that are not the dimension's key, and are not part of a hierarchy.

SML supports the following types of secondary attributes:

- **Dimensional:** Provides an independent "dimensional" attribute for
  grouping metric data. This is the default type of secondary attribute.
- **Level alias:** Enables the creation of tabular reports that select
  hierarchical expressions without forcing the user to drill down a
  hierarchy.

:::note
*Note:* Secondary attributes cannot be used to create relationships
between datasets and dimensions.
:::

## aliases

- **Type:** array
- **Required:** N

Defines secondary attributes that can be used as aliases for specific hierarchy levels within BI tools.

## metrics

- **Type:** array
- **Required:** N

Defines metrics for the level. For more information

# Secondary Attributes Properties

## unique_name

- **Type:** string
- **Required:** Y

The unique name of the secondary attribute. This must be unique within the dimension.

## label

- **Type:** string
- **Required:** Y

The name of the secondary attribute, as it appears in the BI consumer. This value does not need to be unique.

## description

- **Type:** string
- **Required:** N

A description of the secondary attribute.

## folder

- **Type:** string
- **Required:** N

The name of the folder in which the attribute is displayed in BI tools.

## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the attribute is visible in BI tools. 

Supported values:

- `false` (default)
- `true`

## contains\_unique\_names

- **Type:** boolean
- **Required:** N

Determines whether each member of this attribute has a unique name. Do not enable this functionality if two members have different keys but the same name. 

Supported values:

- `true`
- `false`

## dataset

- **Type:** string
- **Required:** Y
 
The dataset that contains the `key_columns` the secondary attribute is based on.

## name_column

- **Type:** string
- **Required:** Y

The dataset column that the attribute is based on.

## key_columns

- **Type:** array
- **Required:** Y

A list of the key columns that the attribute is based on. If the attribute has a compound key, you should specify all columns that make up the key as a list.

## sort_column

- **Type:** array
- **Required:** N

The column used to sort the attribute's values in result sets. (This only applies to MDX queries.)

## allowed\_calcs\_for_dma

- **Type:** array
- **Required:** N

A list of the calculation types that can be used to create dimensionally modified aggregates for the secondary attribute. Note that when working with a time dimension, you can only define calculation types if the `time_unit` property for the level is set to `day` or longer. For more information on dimensionally modified aggregates

## exclude\_from\_dim_agg

- **Type:** Boolean
- **Required:** N

Excludes this attribute from system generated dimension-only aggregates. This is useful if the attribute contains a large number (millions) of distinct values that you don't want to aggregate.

## exclude\_from\_fact_agg

- **Type:** boolean
- **Required:** N

Excludes this attribute from system generated fact-based aggregates. This is useful if the attribute contains a large number (millions) of distinct values that you don't want to aggregate.

## custom\_empty\_member

- **Type:** array
- **Required:** N

Defines a custom empty member for the attribute. This feature allows fact data with missing or invalid foreign key values to be isolated and independently aggregated from those with valid foreign key values. Because fact records with invalid foreign keys are aggregated separately from records referencing valid dimension members, analysts can easily spot data integrity problems and further investigate them. Use this feature to ensure that un-joinable values are included in query results and aggregated under a specially designated dimension member called the Custom Empty Member.

# Alias Properties

## unique_name

- **Type:** String
- **Required:** Y

The unique name of the alias. This must be unique within the dimension.

## label

- **Type:** String
- **Required:** Y

The name of the alias as it appears in the consunmption tool and BI tools. This value does not need to be unique.

## description

- **Type:** String
- **Required:** N

A description of the alias.

## folder

- **Type:** String
- **Required:** N

The name of the folder in which the alias appears in BI tools.
 
## format

- **Type:** String
- **Required:** N

The format in which query results are returned. You can use one of SML's built-in named formats or a custom format string. 

Supported named formats: 

`fixed`,`general number`, `none`, `percent`, `scientific`, `standard` 

Custom format strings should be in quotes and contain one to four sections, separated by semicolons. For more information on defining custom format strings.

## dataset

- **Type:** String
- **Required:** Y

The source dataset that contains the column that the alias is based on.

## name_column

- **Type:** String
- **Required:** Y

The dataset column that the alias is based on.

## sort_column

- **Type:** String
- **Required:** Y

The column to use to sort the values in result sets. This applies to MDX queries only (queries received through the XMLA interface).

## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the alias is visible in BI tools.

Supported values:

- `true`
- `false`

## exclude\_from\_dim_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated dimension-only
aggregates. This is useful if the alias contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## exclude\_from\_fact_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated fact-based
aggregates. This is useful if the alias contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## custom\_empty\_member

- **Type:** object
- **Required:** N

Defines custom empty member values for the alias. This feature allows fact data with missing or invalid foreign key values to be isolated and independently aggregated from those with valid foreign key values. Because fact records with invalid foreign keys are aggregated separately from records referencing valid dimension members, analysts can easily spot data integrity problems and further investigate them. Use this feature to ensure that un-joinable values are included in query results and aggregated under a specially designated dimension member called the Custom Empty Member.


# Metrical Attribute Properties

## unique_name

- **Type:** String
- **Required:** Y

The unique name of the metrical attribute. This must be unique within the dimension.

## label

- **Type:** String
- **Required:** Y

The name of the metrical attribute as it appears in the consunmption tool. This value does not need to be unique.

## description

- **Type:** String
- **Required:** N

A description of the metrical attribute.

## folder

- **Type:** String
- **Required:** N

The name of the folder in which the metrical attribute appears in BI tools.

## format

- **Type:** String
- **Required:** N

The format in which query results are returned. You can use one of SML's built-in named formats or a custom format string. 

Supported named formats: 

`fixed`,`general number`, `none`, `percent`, `scientific`, `standard` 

Custom format strings should be in quotes and contain one to four sections, separated by semicolons. For more information on defining custom format strings.

## dataset

- **Type:** String
- **Required:** Y

The source dataset that contains the column that the metrical attribute is based on.

## column

- **Type:** String
- **Required:** Y

The dataset column that the metrical attribute is based on.

## calculation_method
- **Type:** string
- **Required:** Y

The calculation to apply to the data. 

Supported values: 

`average`, `count distinct`,`count non-null`, `estimated count distinct`, `maximum`, `minimum`,
  `percentile`, `stddev_pop`, `stddev_samp`, `sum`, `var_pop`,`var_samp`
  
## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the alias is visible in BI tools.

Supported values:

- `true`
- `false`

## exclude\_from\_dim_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated dimension-only
aggregates. This is useful if the metrical attribute contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## exclude\_from\_fact_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated fact-based
aggregates. This is useful if the metrical attribute contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## custom\_empty\_member

- **Type:** object
- **Required:** N

Defines custom empty member values for the metrical attribute. This feature allows fact data with missing or invalid foreign key values to be isolated and independently aggregated from those with valid foreign key values. Because fact records with invalid foreign keys are aggregated separately from records referencing valid dimension members, analysts can easily spot data integrity problems and further investigate them. Use this feature to ensure that un-joinable values are included in query results and aggregated under a specially designated dimension member called the Custom Empty Member.

## unrelated\_dimensions\_handling

- **Type:** enum
- **Required:** N

Determines how the query engine behaves when all of the following conditions are true:

- A client queries a model that contains multiple fact datasets.
- The data in each fact dataset are at a different level of
  granularity than the data in the other fact datasets.
- The query references dimensions that are not related to the
  metrics being queried.

Supported values:

> - `error`: Query Engine rejects the query and returns an error message.
> - `empty`: Query Engine displays empty cells in the query results.
> - `repeat`: In the query results, Query Engine repeats the values for
>   the secondary metrical attribute at a level of aggregation that
>   is determined from the shared dimensions in the query.

# Level Attributes Properties

## unique_name

- **Type:** string
- **Required:** Y

The unique name of the level attribute. This must be unique within the
dimension.

## label

- **Type:** string
- **Required:** Y

The name of the level attribute, as it appears in the consunmption tool. This value
does not need to be unique.

## dataset

- **Type:** string
- **Required:** Y

The source dataset that contains the columns that this level attribute
is based on.

## name_column

- **Type:** string
- **Required:** Y

The column whose values appear for this level in BI tools. For example,
the key may be a product ID number, but you want users to see product
names instead.

## key_columns

- **Type:** array
- **Required:** Y

The dataset column that the level attribute is based on. If the level
has a compound key, list all columns that make up the key.

If the key consists of one column, the values in that column must be
unique. If the key is a compound key, the columns together must provide
unique values.

## description

- **Type:** string
- **Required:** N

A description of the level attribute.

## is_hidden

- **Type:** boolean
- **Required:** N

Determines whether the level attribute is visible in BI tools.

Supported values:

- `true`
- `false`

## is\_unique\_key

- **Type:** boolean
- **Required:** N

Determines whether the `key_columns` values are unique for each row.

Supported values:

- `true`: The key column values are unique for each row. The join
  behavior considers the first matching row at query runtime.
- `false`: The key column values are multi-valued. The join behavior
  considers all matching rows at query runtime.

## contains\_unique\_names

- **Type:** boolean
- **Required:** N

Determines whether each member of this level attribute has a unique
name. Do not enable this functionality if two members have different
keys but the same name.

Supported values:

- `true`
- `false`

## exclude\_from\_dim_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated dimension-only
aggregates. This is useful if the attribute contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## exclude\_from\_fact_agg

- **Type:** boolean
- **Required:** N

Excludes this level attribute from system generated fact-based
aggregates. This is useful if the attribute contains a large number
(millions) of distinct values that you don't want to aggregate.

Supported values:

- `true`
- `false`

## sort_column

- **Type:** string
- **Required:** N

Defines the column to sort query results on. By default, this is the
`name_column`; however, you can optionally use this property to specify
a different column.

:::note
*Note:* This only applies to MDX queries (queries received through the
XMLA interface).
:::

## allowed\_calcs\_for_dma

- **Type:** array
- **Required:** N

A list of the calculations that can be used when creating dimensionally
modified aggregates for the level attribute.

## folder

- **Type:** string
- **Required:** N

The name of the folder in which this level attribute appears in BI
tools.

## time_unit

- **Type:** string
- **Required:** N

For time dimensions only. The unit of time to use.

Supported values: 

`year`, `halfyear`, `trimester`, `quarter`, `month`,
`week`, `day`, `hour`, `minute`, `second`, `undefined`

# Custom Empty Member Properties

## key

- **Type:** array
- **Required:** Y
 
A list of the empty member values to use for key fields.

## name

- **Type:** string
- **Required:** Y

The empty member value to use for name fields.

## sort

- **Type:** string
- **Required:** N

The empty member value to use for the attribute's sort column, if one is specified.

