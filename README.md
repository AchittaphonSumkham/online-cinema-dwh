# Online Cinema Data Warehouse

A university Big Data project: an Oracle-based ETL pipeline for an online cinema ticket-booking system, consolidating data from two source systems into a central data warehouse, visualized with Power BI.

## Architecture

```
cinema_1 (source A) ──┐
                      ├──> Staging (ST_*) ──db link──> Data Warehouse (DW_*) ──> Power BI
cinema_2 (source B) ──┘
```

- **Two source systems** (`cinema_1`, `cinema_2`) with the same business domain but separate schemas — members, movies, showtimes, tickets, events.
- **Staging layer** (`ST_*` tables) merges both sources using `MERGE` statements, tagging each row with `SourceSystem`.
- **Data warehouse** (`DW_*` tables) uses surrogate keys (sequences + triggers) and `EffectiveDate`/`EndDate` columns for slowly changing dimensions, loaded over an Oracle database link.

See `er_diagram.png` for the full entity-relationship diagram.

## Repository structure

| Path | Description |
|------|-------------|
| `SQL/Cinema_1.txt` | DDL + PL/SQL seed data for source system 1 |
| `SQL/Cinema_2.txt` | DDL + PL/SQL seed data for source system 2 (`*_B2` tables) |
| `SQL/Staging.txt` | Staging tables, sequences/triggers, MERGE from both sources, load into DW |
| `SQL/Datawarehouse.txt` | Database link + warehouse tables with SCD columns |
| `SQL/PowerBI_*.pbix` | Power BI dashboards |
| `DML/*.xlsx` | Source data, imported into the source tables via DBeaver |
| `er_diagram.png` | ER diagram of the whole system |

## Running

Execute against Oracle in this order, each in its own schema/user:

1. `SQL/Cinema_1.txt` and `SQL/Cinema_2.txt` — create and seed the source systems (or import `DML/*.xlsx` via DBeaver).
2. `SQL/Datawarehouse.txt` — create the warehouse tables (fill in the database link placeholders `<username>`, `<password>`, `<db_host>`, `<service_name>` with your own connection details).
3. `SQL/Staging.txt` — create the staging layer, run the MERGEs, then load the warehouse.
4. Open the `.pbix` files in Power BI Desktop and point them at your warehouse.
