# Streaming Platform: Design from Scratch

## Scenario
A streaming platform (similar to Spotify) requires analytical capabilities to answer the following business questions:

- Number of plays per song per day and per month  
- Top artists by country  
- User retention (e.g., week-over-week)  
- Average session duration per user  

## Task
Design a Data Warehouse schema that supports these analytics. The following design decisions must be addressed:

1. **Fact table design** – Should separate fact tables be created for “play started” and “play completed” events, or can a single fact table cover both? Provide a rationale.

2. **Playlist modeling** – Playlists and songs have a many‑to‑many relationship. How should this be represented in the DWH to enable analytical queries (e.g., playlist popularity, collaborative filtering) while maintaining performance?

3. **Pre‑aggregated tables** – Which aggregates are worth pre‑computing in dedicated tables to speed up reporting? For at least three proposed aggregates, specify:
   - Granularity (e.g., daily, country‑level)  
   - Refresh frequency and strategy (incremental vs. full rebuild)

*The solution should include a clear description of table structures, key relationships, and trade‑offs considered.*
