# PA-03 — Data, Time & Instrument Semantics

Survey established approaches for:
- instrument identifiers;
- exchanges/venues;
- trading calendars;
- sessions and holidays;
- time zones/DST;
- corporate actions;
- dividends/splits;
- futures contracts and continuous series;
- roll rules and back-adjustment;
- FX conventions;
- tick/lot/contract metadata;
- currencies and FX conversion;
- resampling;
- multi-timeframe synchronization;
- point-in-time availability;
- missing/late data;
- revisions;
- schema evolution.

Candidates may include:
- Apache Arrow / Parquet ecosystems;
- exchange-calendars / pandas-market-calendars;
- Polars / pandas / DuckDB;
- mature trading engines' instrument/calendar models;
- standards and vendor schemas.

This stream directly informs DataSnapshot and StrategyIR contracts.
