# WordPress Access Manifest

**A standard for declaring access intent in WordPress plugins and themes.**

WordPress Access Manifest defines a **machine-readable format** for describing the access-controlled surfaces a plugin or theme introduces and the access assumptions made by its author.

It is **declarative**, **non-enforcing**, and designed to be consumed by tooling such as security audits, access governance systems, and AI-based code analysis.

---

## Why This Exists

WordPress plugins and themes frequently introduce:

* Admin menus
* REST API routes
* AJAX actions
* Blocks, shortcodes, settings, cron jobs

These surfaces are usually protected by **implicit capability checks** (`manage_options`, `edit_posts`, etc.), but WordPress provides **no standard way to declare this intent**.

As a result:

* Access design is undocumented
* Least-privilege is hard to implement
* Security audits rely on heuristics
* Tooling must infer behavior from code

The Access Manifest solves this by allowing authors to **declare access intent explicitly**, without changing runtime behavior.

---

## What the Manifest Is (and Is Not)

### What it is

* A **declaration of access intent**
* A **static contract** between authors and tools
* A foundation for audits, governance, and automation
* Optional and additive

### What it is not

* An access enforcement mechanism
* A replacement for WordPress roles or capabilities
* A security policy engine
* A requirement for plugin approval or distribution

---

## How It Works

Plugins or themes may include an `access.json` file at their root:

```
my-plugin/
  access.json
  my-plugin.php
```

This file describes:

* Access-controlled surfaces (menus, routes, actions, etc.)
* Default capability assumptions
* Custom capabilities and roles (if any)

Tools may then:

* Read and validate the manifest
* Compare declared intent with runtime behavior
* Recommend least-privilege models
* Generate audit or governance reports

---

## Minimal Example

```json
{
  "schema": "https://aamplugin.com/schemas/access-manifest-1.0.schema.json",
  "provider": {
    "name": "My Plugin",
    "slug": "my-plugin",
    "type": "plugin"
  },
  "surfaces": {
    "admin_menus": [
      {
        "id": "my_plugin_settings",
        "label": "My Plugin Settings",
        "capability": "manage_options"
      }
    ]
  }
}
```

This declares:

* The plugin introduces an admin menu
* The author assumes `manage_options` by default
* No enforcement behavior is implied

---

## Supported Surface Types (v1.0)

* `admin_menus`
* `admin_toolbar`
* `rest_routes`
* `ajax_actions`
* `meta_boxes`
* `blocks`
* `shortcodes`
* `settings`
* `cron_jobs`

Each surface is identified by a stable `id` and may declare a default capability and metadata.

---

## Design Principles

1. **Intent over implementation**
   The manifest describes *what exists*, not *how it is enforced*.

2. **Surfaces first**
   Capabilities are implementation details; surfaces are what matter.

3. **Non-enforcing by design**
   Presence of a manifest MUST NOT affect runtime behavior.

4. **Partial adoption is valid**
   Authors may declare only some surfaces.

5. **Tool-friendly**
   Optimized for static analysis and AI consumption.

---

## Validation

A formal JSON Schema is provided:

```
schema/access-manifest-1.0.schema.json
```

Validation ensures:

* Correct structure
* Valid surface definitions
* Stable identifiers

Validation does **not** verify:

* Runtime existence of surfaces
* Correctness of access logic
* Security posture

---

## Adoption Strategy

The Access Manifest is:

* Optional
* Backward compatible
* Designed for gradual adoption

Tools MAY:

* Warn about missing or incomplete declarations
* Highlight mismatches between declared and actual access
* Suggest least-privilege improvements

Tools MUST NOT:

* Enforce access solely based on the manifest
* Assume declared access is correct

---

## Tooling & Reference Implementation

Advanced Access Manager (AAM) provides a reference implementation that:

* Detects `access.json`
* Imports declared surfaces
* Compares intent with runtime behavior
* Uses the manifest for access audits and governance

Other tools are encouraged to implement support.

---

## Status

* **Specification:** Draft v1.0
* **Stability:** Intended to be stable and additive
* **Feedback:** Actively encouraged

---

## Contributing

Feedback, issues, and proposals are welcome.

When contributing:

* Prefer additive changes
* Avoid enforcement semantics
* Keep WordPress realities in mind

---

## License

This specification is open and may be freely implemented, extended, or referenced.