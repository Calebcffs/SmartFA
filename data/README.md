# data/

Source of truth for every fact the site shows. JSON only, validated against the Zod schemas in `engine/src/schema/`. Every record carries a provenance block (source_url, retrieved_at, last_verified, verified_by). See ../ARCHITECTURE.md §3.

- `providers/`: one file per provider (card C03)
- `products/<provider>/`: one file per product (cards C05-<provider>)
- `schemes/`, `constants/`: government schemes and dated constants (card C06)
