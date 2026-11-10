- When writing a new or changed line, use full words: no abbreviations, and no single-character names except `_` (`index`, `error`, `candidate`). Names such as `qty`, `amt` and `cust_ref` make the reader guess; spell them out, even when the name gets longer.
- When a value has a unit, put the unit in the name, or in a trailing comment when a contract fixes the name: `UPLOAD_TIMEOUT_SECONDS`, `EXPORT_CHUNK_SIZE_BYTES`, `max_file_size_mb`, `travel_distance  # mm`. Convert with the true unit (`* 1000 * 1000` for MB; say MiB for 1024) and delete unit-less aliases.
- When a name could be ambiguous, add the critical context: `ingress_subnet`, not `subnet`.
- When the origin of a value matters, name it for its origin: `viewerEmail`, so every call site is forced through the server-resolved value and the name says where it comes from; `requester_access_policy`.
- When naming a function, use a verb phrase, because functions perform actions. A name like `process_items` is too general.
- When a helper or a table is private to a module, prefix it with an underscore. Name a lookup table for its key and value (`_REQUIRED_SETTINGS_BY_BACKEND`) and a test helper for what it builds (`minimal_valid_payload`).
- When an existing name is misleading, propose the rename and make it repository-wide in one commit that lists the old and new names. Never rename for taste.

This is stricter than PEP 8 on names. The touched-lines scope is the Boy Scout Rule applied narrowly.
