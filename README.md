![logo](images/sml-logo-large.png)

# What is SML?
Semantic Modeling Language, or SML for short, encompasses over a decade of hands-on development, solving use cases for hundreds of customers across industries such as finance, healthcare, retail, manufacturing, CPG, and more. SML covers more than just tabular use cases. At its core, it is a multidimensional semantic modeling language that supports metrics, dimensions, hierarchies,  semi-additive measures, many-to-many relationships, cell-based expressions, and much more. 

SML delivers on the following requirements:

1. **Object-oriented**: SML is an object-oriented language that promotes composability and inheritance. This allows semantic objects to be shared within other semantic objects and across organizations, supporting easy and consistent model-building.
2. **Comprehensive**: SML is based on more than a decade of modeling experience across various industry verticals and use cases. SML handles multi-dimensional constructs and serves as a superset of all other existing semantic modeling languages.
3. **Familiar**: SML is based on YAML, a widely adopted, human-readable, industry-standard syntax.
CI/CD Friendly: SML is code, so it is compatible with Git and CI/CD practices for version control, automated deployment, and software lifecycle management. 
4. **Extensible**: SML syntax can be enhanced to support additional properties and features.
Open: SML is Apache open-sourced to support community innovation and is free to use in any application or use case.

## SML Example
The following is an example of an SML `model` object:

```unique_name: Internet Sales
object_type: model
label: Internet Sales
visible: true

relationships:
  - unique_name: factinternetsales_Date_Dimension_Order
    from:
      dataset: factinternetsales
      join_columns:
        - orderdatekey
    to:
      dimension: Date Dimension
      level: DayMonth
    role_play: "Order {0}"

dimensions:
  - Color Dimension
  - Size Dimension
  - Style Dimension
  - Weight

metrics:
  - unique_name: orderquantity
    folder: Sales Metrics

  - unique_name: salesamount
    folder: Sales Metrics
```

## SML Object Hierarchy
The following graphic illustrates the key SML objects and their relationships:

```mermaid
classDiagram
    relationship --* from : Contains
    from ..> Dataset : Depends
    from ..> Column : Depends
    relationship --* to : Contains
    to ..> Dimension : Depends
    to ..> Level : Depends
    to ..> RowSecurity : Depends
    perspective ..> Metric : Depends
    perspective ..> MetricCalc : Depends
    perspective ..> Dimension : Depends
    perspective ..> Hierarchy : Depends
    perspective ..> LevelAttribute : Depends
    perspective ..> SecondaryAttribute : Depends
    perspective ..> Alias : Depends
    perspective ..> MetricalAttribute : Depends
    drillthrough ..> Metric : Depends
    drillthrough ..> MetricCalc : Depends
    drillthrough ..> LevelAttribute : Depends
    drillthrough ..> SecondaryAttribute : Depends
    drillthrough ..> Alias : Depends
    drillthrough ..> MetricalAttribute : Depends
    aggregate ..> Connection : Depends
    aggregate ..> Metric : Depends
    aggregate ..> MetricCalc : Depends
    aggregate ..> LevelAttribute : Depends
    aggregate ..> SecondaryAttribute : Depends
    aggregate ..> Alias : Depends
    aggregate ..> MetricalAttribute : Depends
    partition ..> Dimension : Depends
    partition ..> LevelAttribute : Depends
    partition ..> SecondaryAttribute : Depends
    partition ..> Alias : Depends
    Model ..> Dimension : Depends
    Model ..> Metric : Depends
    Model *-- relationship : Contains
    Model *-- perspective : Contains
    Model *-- aggregate : Contains
    Model *-- partition : Contains
    Model *-- drillthrough : Contains
    Dataset ..> Connection : Depends
    Dataset *-- Column : Contains
    Dataset *-- Alternate : Contains
    Dataset *-- Incremental : Contains
    Alternate ..> Connection : Depends
    Incremental ..> Column : Depends
    Column *-- MapColumn : Contains
    Metric ..> Dataset : Depends
    Metric ..> Column : Depends
    Metric *-- SemiAdditive : Contains
    SemiAdditive ..> relationship : Depends
    Dimension *-- Hierarchy : Contains
    Dimension *-- LevelAttribute : Contains
    Dimension *-- relationship : Contains
    Dimension *-- CalculationGroup : Contains
    Hierarchy *-- Level : Contains
    Level ..> LevelAttribute : Depends
    LevelAttribute ..> Dataset : Depends
    LevelAttribute ..> Column : Depends
    LevelAttribute *-- CustomEmptyMember : Contains
    Level ..> LevelAttribute : Depends
    Level *-- SecondaryAttribute : Contains
    Level *-- MetricalAttribute : Contains
    Level *-- Alias : Contains
    CalculationGroup *-- DimensionMetricCalc : Contains
    SecondaryAttribute ..> Dataset : Depends
    SecondaryAttribute ..> Column : Depends
    SecondaryAttribute *-- CustomEmptyMember : Contains
    Alias ..> Dataset : Depends
    Alias ..> Column : Depends
    Alias *-- CustomEmptyMember : Contains
    MetricalAttribute ..> Dataset : Depends
    MetricalAttribute ..> Column : Depends
    MetricalAttribute *-- CustomEmptyMember : Contains    
    MetricCalc ..> MetricCalc : Depends
    MetricCalc ..> Metric : Depends
    MetricCalc ..> Hierarchy : Depends
    MetricCalc ..> Level : Depends
    MetricCalc ..> SecondaryAttribute : Depends
    RowSecurity ..> Dataset : Depends
    RowSecurity ..> Column : Depends    
namespace Models{
    class Model{
      String unique_name
      const object_type
      String label
      String description
      Array~relationship~ relationships
      Array~String~ dimensions
      Array~metric_reference~ metrics
      Array~aggregate~ aggregates
      Array~perspective~ perspectives
      Array~drillthrough~ drillthroughs
      Array~partition~ partition
      Boolean include_default_drillthrough
    }
    class relationship{
      String unique_name
      Object from
      Object to
      String role_play
      String type
      Boolean m2m
    }
    class from{
      String dataset
      Array~Column~ columns
    }
    class to{
      String dimension
      String level
      String row_security
    }
    class aggregate{
      String unique_name
      String label
      String target_connection
      Array~String~ metrics
      Array~attribute_reference~ attributes
    }
    class drillthrough{
      String unique_name
      String notes
      Array~String~ metrics
      Array~attribute_reference~ attributes
    }
    class attribute_reference{
      String name
      String dimension
      String partition
      String distribution
    }
    class partition{
      String unique_name
      String dimension
      String attribute
      String type
    }
    class perspective{
      String unique_name
      Array~String~ metrics
      Array~perspective_dimension~ dimensions
    }
    class metric_reference{
      String unique_name
      String folder
    }
    class perspective_dimension{
      String name
      Boolean visible
      Array~perspective_hierarchy~ hierarchies
      Array~String~ secondaryattributes
      Array~String~ metrics
    }
    class perspective_hierarchy{
      String name
      Boolean visible
      Array~String~ levels
    }
}
namespace Datasets{    
    class Dataset{
      String unique_name
      const object_type
      String label
      String description
      String connection_id
      String sql
      String table
      Array~Column~ columns
      Array~Dialect~ dialects
      Boolean immutable
      Alternate alternate
      Incremental incremental
    }
    class Column{
      String name
      const data_type
      String sql
      Array~Dialect~ dialects
      MapColumn map
    }
    class Dialect{
      String dialect
      String sql
    }
    class Alternate{
      String type
      String connection_id
      String table
      String sql      
    }
    class Incremental{
      String column
      String grace_period
    }
    class MapColumn{
      String field_terminator
      String key_terminator
      String key_type
      String value_type
      Boolean is_prefixed
    }
}
namespace Connections{
    class Connection{
      String unique_name
      String label
      const object_type
      String as_connection
      String database
      String schema
    }
}
namespace Metrics{
    class Metric{
      String unique_name
      String label
      const object_type
      String description
      String calculation_method
      String dataset
      String column
      SemiAdditive semi_additive
      int compression
      String named_quantiles
      Array~double~ custom_quantiles
      String format
      enum unrelated_dimensions_handling
      Boolean is_hidden
    }
    class SemiAdditive{
      String position
      Array~Array~String~~ relationships
    }
    class MetricCalc{
      String unique_name
      String label
      String description
      const object_type
      String format
      String expression
      String default_aggregation
    }
}
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
      Array~relationship~ relationships
      Array~CalculationGroup~ calculation_groups
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
      Array~DimensionMetricCalc~ calculated_members
    }
    class DimensionMetricCalc{
      String name
      String description
      String format
      String expression
      Boolean is_default
      Boolean use_input_measure_format
    }
}
namespace Row_Security{
    class RowSecurity{
      String unique_name
      String label
      const object_type
      String description
      String dataset
      String filter_key_column
      Boolean use_filter_key
      String ids_column
      String id_type
      String scope
      Boolean secure_totals
    }
}
```

## SML Object Documentation

The following sections describe the different SML object types as well
as the properties available for each:

- [Catalog](sml-reference/catalog.md)
- [Package](sml-reference/package.md)
- [Calculations](sml-reference/calculations.md)
- [Connections](sml-reference/connections.md)
- [Datasets](sml-reference/datasets.md)
- [Dimensions](sml-reference/dimensions.md)
- [Metrics](sml-reference/metrics.md)
- [Models](sml-reference/models.md)
- [Row Security](sml-reference/row-security.md)

## What's in this repository?

Open-sourcing SML aims to promote the building of reusable models and semantic objects. We are making the SML specification available for public consumption and collaboration. Soon, we will add software tools to make serializations and translations from various semantic dialects easier.

We are or will be open-sourcing the following:

1. **A YAML-based Language Specification**: The SML specification is documented and encompasses tabular and multidimensional constructs.
2. **Pre-built Semantic Models**: The GitHub repository contains pre-built semantic models incorporating standard data models such as TPC-DS, common training models such as Worldwide Importers and AdventureWorks, and marketplace models such as Snowplow and CRISP. We expect to add semantic models for SaaS applications such as Salesforce, Google Analytics, and Jira soon.
3. **Helper Classes** *(coming soon)*: We will release helper classes that will facilitate the programmatic reading and writing of SML syntax.
4. **Semantic Translators** *(coming soon)*: We will release converters for migrating other semantic modeling languages to SML, including dbt Lab’s semantic layer and Power BI. Shortly, we expect to release a variety of converters to support the legacy (i.e., Microstrategy, Business Objects, Cognos) and modern (i.e. Looker) semantic modeling tools. 
