[TOC]

# Workloadvendor

## Top-Level Attributes

Here are the attributes that provide top-level information about the workload definition.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `apiVersion` | `string` | Y | | foo.com/v1alpha1 |
| `kind` | `string` | Y || Must be `Workloadvendor` |
| `metadata` | [`Metadata`](#metadata) | Y | | Entity metadata. |
| `spec`| [`Spec`](#spec) | Y | | The specification for the workload definition. |

### Metadata

This metadata section is made up of several top-level keys.

Metadata provides information about the contents of the object.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `name` | `string` | Y | | vendor name |
| `annotations` | `map[string]string`| N | |  |

#### Spec

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|