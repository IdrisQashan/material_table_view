# Material Table View local dependency

Origin: `material_table_view` 5.5.2 from pub.dev, MIT licensed.
Upstream: https://github.com/NikolayNIK/material_table_view

The application resolves this package through its explicit path dependency.
Only the upstream `lib/`, license, and package manifest are retained. Do not edit
the global Pub cache or introduce an application-level replacement table engine.

## Lifecycle correction

`_TableViewRowElement.didChangeDependencies` previously reconciled children
synchronously. During GlobalKey reparenting, Flutter calls that method from
`Element.activate`, **before** recursively activating the old children. The
reconciliation could mount new active children into a list Flutter then tries
to activate, causing the inactive-element assertion. Its debug-only skip also
left debug and release behavior different.

Use the inherited dependency invalidation to schedule `performRebuild`, the
existing owner of render-object and cell reconciliation. All descendants are
active by that time. Remove the obsolete debug-only inactive-child workaround.
No table geometry, API, column ownership, or row state contracts change.

Retire this local dependency only when an upstream release contains the same
fix and passes the reparent-with-changed-columns regression. Preserve the
regression when updating; do not solve it by throwing away collection state.
