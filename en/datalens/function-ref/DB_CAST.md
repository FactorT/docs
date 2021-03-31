---
editable: false
---

# DB_CAST



#### Syntax {#syntax}


```
DB_CAST( expression, native_type [ , param_1 [ , param_2 ] ] )
```

#### Description {#description}
Converts the `expression` expression to database's native type `native_type`.

The following type casts are supported:

| Data source   | Data type           | Native data type    | Parameters for native type   | Comment                       |
|:--------------|:--------------------|:--------------------|:-----------------------------|:------------------------------|
| `ClickHouse`  | `Fractional number` | `Float32`           |                              |                               |
| `ClickHouse`  | `Fractional number` | `Float64`           |                              |                               |
| `ClickHouse`  | `Fractional number` | `Decimal`           | `Integer`, `Integer`         |                               |
| `ClickHouse`  | `Integer`           | `Int8`              |                              |                               |
| `ClickHouse`  | `Integer`           | `Int16`             |                              |                               |
| `ClickHouse`  | `Integer`           | `Int32`             |                              |                               |
| `ClickHouse`  | `Integer`           | `Int64`             |                              |                               |
| `ClickHouse`  | `String`            | `String`            |                              |                               |
| `PostgreSQL`  | `Fractional number` | `double precision`  |                              |                               |
| `PostgreSQL`  | `Fractional number` | `real`              |                              |                               |
| `PostgreSQL`  | `Fractional number` | `numeric`           | `Integer`, `Integer`         |                               |
| `PostgreSQL`  | `Integer`           | `smallint`          |                              |                               |
| `PostgreSQL`  | `Integer`           | `integer`           |                              |                               |
| `PostgreSQL`  | `Integer`           | `bigint`            |                              |                               |
| `PostgreSQL`  | `String`            | `text`              |                              |                               |
| `PostgreSQL`  | `String`            | `character`         | `Integer`                    | Alias: `char`                 |
| `PostgreSQL`  | `String`            | `character varying` | `Integer`                    | Alias: `varchar`              |
| `PostgreSQL`  | `String`            | `char`              | `Integer`                    | Alias for `character`         |
| `PostgreSQL`  | `String`            | `varchar`           | `Integer`                    | Alias for `character varying` |


**Argument types:**
- `expression` — `Fractional number | Integer | String`
- `native_type` — `String`
- `param_1` — `Any`
- `param_2` — `Any`


**Return type**: Same type as (`expression`)

{% note info %}

Only constant values are accepted for arguments (`native_type`).

{% endnote %}


#### Examples {#examples}

```
DB_CAST([float_value], "Decimal", 10, 5)
```

```
DB_CAST([float_value], "double precision")
```

```
DB_CAST([float_value], "numeric", 10, 5)
```


#### Data source support {#data-source-support}

`Materialized Dataset`, `ClickHouse 1.1`, `PostgreSQL 9.3`.