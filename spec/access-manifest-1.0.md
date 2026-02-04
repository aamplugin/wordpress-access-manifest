# WordPress Access Manifest

**Version:** 1.0
**Status:** Draft
**Maintained by:** Advanced Access Manager (AAM)
**Scope:** WordPress plugins and themes

---

## 1. Purpose

The Access Manifest provides a **standardized, machine-readable way** for WordPress plugins and themes to declare the **access-controlled surfaces** they introduce and the **access assumptions** they make.

The manifest:

* **Declares intent**, not enforcement
* Enables tooling (e.g. AAM, AI agents, audits) to understand access design
* Improves least-privilege modeling and governance
* Does not change WordPress runtime behavior

---

## 2. Design Principles

1. **Declarative, not imperative**
   The manifest describes *what exists*, not *how it is enforced*.

2. **Surfaces first**
   Access is defined in terms of *surfaces* (menus, routes, actions), not roles.

3. **Non-enforcing by default**
   Presence of a manifest MUST NOT alter runtime access behavior.

4. **Optional and additive**
   Plugins/themes may partially declare access surfaces.

5. **Tool-friendly**
   Must be statically analyzable and AI-readable.

---

## 3. File Location & Format

### 3.1 File Name

```
access.json
```

### 3.2 Location

Plugin or theme root directory.

### 3.3 Format

* UTF-8 encoded JSON
* No comments
* JSON Schema compliant

---

## 4. Top-Level Structure

```json
{
  "schema": "https://aamportal.com/schemas/access-manifest-1.0.json",
  "provider": {},
  "surfaces": {},
  "capabilities": [],
  "roles": []
}
```

---

## 5. Provider Object

Identifies the declaring plugin or theme.

```json
"provider": {
  "name": "My Plugin",
  "slug": "my-plugin",
  "type": "plugin",
  "version": "2.1.0"
}
```

### Fields

| Field   | Type   | Required | Description         |
| ------- | ------ | -------- | ------------------- |
| name    | string | yes      | Human-readable name |
| slug    | string | yes      | WordPress slug      |
| type    | string | yes      | `plugin` or `theme` |
| version | string | no       | Provider version    |

---

## 6. Surfaces

Surfaces represent **user-accessible or executable entry points** that require access control.

### 6.1 Surface Categories

```json
"surfaces": {
  "admin_menus": [],
  "admin_toolbar": [],
  "rest_routes": [],
  "ajax_actions": [],
  "meta_boxes": [],
  "blocks": [],
  "shortcodes": [],
  "settings": [],
  "cron_jobs": []
}
```

Each category is optional.

---

### 6.2 Common Surface Fields

All surface definitions MAY include:

| Field              | Type    | Required | Description                         |
| ------------------ | ------- | -------- | ----------------------------------- |
| id                 | string  | yes      | Unique identifier within provider   |
| label              | string  | no       | Human-readable label                |
| capability         | string  | no       | Capability assumed by the developer |
| required           | boolean | no       | If true, access must not be removed |
| enforcement        | string  | no       | `wp`, `custom`, or `unknown`        |

---

### 6.3 Admin Menu Surface

```json
{
  "id": "my_plugin_settings",
  "label": "My Plugin Settings",
  "path": "options-general.php?page=my-plugin",
  "capability": "manage_options",
  "enforcement": "wp"
}
```

---

### 6.4 Admin Toolbar Surface

```json
{
  "id": "my_plugin_toolbar",
  "label": "My Plugin",
  "capability": "manage_options"
}
```

---

### 6.5 REST Route Surface

```json
{
  "namespace": "my-plugin/v1",
  "route": "/settings",
  "methods": ["GET", "POST"],
  "capability": "manage_options"
}
```

---

### 6.6 AJAX Action Surface

```json
{
  "id": "my_plugin_save_settings",
  "action": "my_plugin_save",
  "capability": "manage_options"
}
```

---

### 6.7 Meta Box Surface

```json
{
  "id": "my_plugin_meta",
  "post_types": ["post", "page"],
  "capability": "edit_posts"
}
```

---

### 6.8 Block Surface

```json
{
  "id": "my-plugin/secure-block",
  "capability": "edit_posts"
}
```

---

### 6.9 Shortcode Surface

```json
{
  "id": "my_plugin_shortcode",
  "tag": "my_plugin",
  "capability": "read"
}
```

---

### 6.10 Setting Surface

```json
{
  "id": "my_plugin_api_key",
  "group": "my_plugin_settings",
  "capability": "manage_options"
}
```

---

### 6.11 Cron Job Surface

```json
{
  "id": "my_plugin_sync",
  "schedule": "hourly",
  "capability": "manage_options"
}
```

---

## 7. Capabilities

Declares **custom capabilities introduced or expected** by the provider.

```json
"capabilities": [
  {
    "id": "my_plugin_manage_settings",
    "description": "Manage plugin settings",
    "used_by": [
      "admin_menus.my_plugin_settings",
      "rest_routes./settings"
    ]
  }
]
```

### Fields

| Field       | Type   | Required |
| ----------- | ------ | -------- |
| id          | string | yes      |
| description | string | no       |
| used_by     | array  | no       |

---

## 8. Roles

Declares **roles introduced by the provider**.

```json
"roles": [
  {
    "id": "my_plugin_manager",
    "display_name": "Plugin Manager",
    "capabilities": [
      "my_plugin_manage_settings"
    ]
  }
]
```

### Fields

| Field        | Type   | Required |
| ------------ | ------ | -------- |
| id           | string | yes      |
| display_name | string | yes      |
| capabilities | array  | no       |

---

## 9. Semantics & Interpretation

* The manifest **does not grant or revoke access**
* `capability` represents the **author’s assumption**
* Tools MAY override declared defaults
* Missing declarations imply **unknown or undeclared access**

---

## 10. Validation Rules

* IDs MUST be unique within their category
* Capabilities referenced MUST be declared or core capabilities
* Surfaces SHOULD map to real WordPress constructs
* Invalid manifests MUST be ignored gracefully

---

## 11. Backward Compatibility

* Absence of `access.json` implies no declaration
* Partial manifests are valid
* Future versions MUST remain additive

---

## 12. Security Considerations

* Manifest data MUST NOT be trusted for enforcement
* Tools SHOULD validate declared vs runtime behavior
* Discrepancies SHOULD be reported, not auto-corrected

---

## 13. Future Extensions (Non-Normative)

* Conditional access expressions
* Multisite scope declarations
* Contextual access (environment, tenant)
* Integration with WP core registry

---

## 14. License

This specification is open and may be freely implemented.