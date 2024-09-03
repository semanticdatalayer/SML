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
    relationship ..> Dataset : Depends
    relationship ..> from : Contains
    from ..> Dataset : Depends
    relationship ..> to : Contains
    to ..> Dimension : Depends
    perspective ..> Metric : Depends
    perspective ..> Dimension : Depends
    drillthrough ..> Metric : Depends
    drillthrough ..> LevelAttribute : Depends
    aggregate ..> Metric : Depends
    aggregate ..> LevelAttribute : Depends
    aggregate ..> Connection : Depends
    partition ..> Dimension : Depends
    partition ..> LevelAttribute : Depends
    Model *-- dimension : Contains
    Model *-- metric : Contains
    Model *-- relationship : Contains
    Model *-- perspective : Contains
    Model *-- aggregate : Contains
    Model *-- partition : Contains
    Model *-- drillthrough : Contains
    dimension ..> Dimension : Depends
    metric ..> Metric : Depends
    Dataset ..> Connection : Depends
    Dataset ..> Column : Depends
    Metric ..> Dataset : Depends
    Metric ..> Column : Depends
    Metric ..> semi_additive : Depends
    semi_additive ..> Dimension : Depends
    semi_additive ..> Hierarchy : Depends
    semi_additive ..> Level : Depends
    Dimension *-- Hierarchy : Contains
    Hierarchy *-- Level : Contains
    Dimension *-- LevelAttribute : Contains
    Dimension *-- relationship : Contains
    Dimension ..> CalculationGroup : Contains
    Level ..> LevelAttribute : Depends
    LevelAttribte ..> Column : Depends
    LevelAttribte ..> MetricCalc : Depends
    Level ..> LevelAttribute : Contains
    Level ..> MetricalAttribute : Contains
    Level ..> Alias : Contains
    CalculationGroup ..> MetricCalc : Depends
    Dimension ..> SecondaryAttribute : Contains
    SecondaryAttribute ..> Dataset : Depends
    SecondaryAttribute ..> Column : Depends
    SecondaryAttribute ..> MetricCalc : Depends
    CustomEmptyMember ..> SecondaryAttribute : Contains
    CustomEmptyMember ..> Column : Depends
    Alias ..> Dataset : Depends
    Alias ..> Column : Depends
    Alias ..> CustomEmptyMember : Contains
    MetricalAttribute ..> CustomEmptyMember : Contains
    MetricalAttribute ..> Dataset : Depends
    MetricalAttribute ..> Column : Depends
namespace Models{
    class Model{
      String unique_name
      const object_type
      String label
      Array~relationship~ relationships
      Array~dimension~ dimensions
      Array~metric~ metrics
      Array~aggregate~ aggregates
      Array~perspective~ perspectives
      Array~drillthrough~ drillthroughs
      Array~partition~ partition
    }
    class relationship{
      String unique_name
      Object from
      Object to
      String role_play
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
      Array~Metric~ metrics
      Array~Attribute~ attributes
    }
    class drillthrough{
      String unique_name
      String notes
      Array~Metric~ metrics
      Array~Attribute~ attributes
    }
    class partition{
      String unique_name
      String dimension
      String attribute
      String type
    }
    class perspective{
      String unique_name
      Array~Metric~ metrics
      Array~Dimension~ dimensions
    }
    class metric{
      String unique_name
      String folder
    }
    class dimension{
      String unique_name
    }
}
namespace Datasets{    
    class Dataset{
      String unique_name
      const object_type
      String label
      String connection_id
      String sql
      String table
      Array~Column~ columns
    }
    class Column{
      String name
      const data_type
      String sql
      Array~Dialect~ dialects
    }
}
    class Connection{
      String unique_name
      String label
      const object_type
      String as_connection
      String database
      String schema
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
      Object semi_additive
      int compression
      String named_quantiles
      String format
      enum unrelated_dimensions_handling
      Boolean is_hidden
    }
    class semi_additive{
      String position
      String dimension
      String hierarchy
      String level
    }
    class MetricCalc{
      String unique_name
      String label
      const object_type
      String format
      String expression
    }
}
namespace Dimensions{
        class Dimension{
          String unique_name
          String label
          const object_type
          String description
          enum type
          Array~Hierarchy~ hierarchies
          Array~LevelAttribute~ level_attributes
          Array~relationship~ relationships
          Array~CalculationGroup~ calculation_groups
        }
        class Hierarchy{
          String unique_name
          String label
          Array~Level~ levels
        }
        class Level{
          String unique_name
          String description
          String folder
          Array~SecondaryAttribute~ secondary_attributes
          Array~Alias~ aliases
          Array~MetricalAttribute~ metrics
          String filter_empty
          String default_member
        }
        class Alias{
          String unique_name
          String description
          String label
          String dataset
          String name_column
          String sort_column
          Boolean is_hidden
          Boolean exclude_from_dim_agg
          Boolean exclude_from_fact_agg
          Array~CustomEmptyMember~ custom_empty_member

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
        }
        class LevelAttribute{
          String unique_name
          String label
          String description
          String dataset
          String name_column
          Array~Column~ key_columns
          Boolean is_hidden
          Boolean is_unique_key
          Boolean contains_unique_names
          Boolean exclude_from_dim_agg
          Boolean exclude_from_fact_agg
          String sort_column
          Array~MetricCalc~ allowed_calcs_for_dma
          String folder
          String time_unit
        }
        class SecondaryAttribute{
          String unique_name
          String label
          String dataset
          String name_column
          Array~Column~ key_columns
          String sort_column
          Array~MetricCalc~ allowed_calcs_for_dma
          Boolean exclude_from_dim_agg
          Boolean exclude_from_fact_agg
          Array~CustomEmptyMember~ custom_empty_member
        }
        class CustomEmptyMember{
          Array~Column~ key
          String name
          String sort
          String description
          String folder
          String format
          Boolean is_hidden
          Boolean contains_unique_names
        }
        class CalculationGroup{
          String unique_name
          String description
          String folder
          Array~MetricCalc~ calculated_members
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
