# Trait

## Trait Definition

This section is normative because traits are an inspectable (and possibly shareable) part of the system. All traits MUST be representable in the following format.

Traits are defined with schematics like components.

### Top-Level Attributes

The top-level attributes of a trait define its metadata, version, kind, and spec.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `apiVersion` | `string` | Y || A string that identifies the version of the schema the object should have. The core types uses `aam.globalsphare.com/v1alpha1` in this version of documentation. |
| `kind` | `string` | Y || Must be `Trait` |
| `metadata` | [`Metadata`](#metadata) | Y | | Information about the trait. |
| `spec`| [`Spec`](#spec) | Y || A specification for trait attributes. |

#### Metadata

This metadata section is made up of several top-level keys.

Metadata provides information about the contents of the object.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `name` | `string` | Y | | A name for the schematic. `name` is subject to the restrictions listed beneath this table. |
| `annotations` | `map[string]string`| N || A set of string key/value pairs used as arbitrary descriptive text associated with this object. See the "Annotations format" section immediately below. |

#### Spec

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `parameter` | [JSON Schema](#https://json-schema.org/) | N | | JSON Schema 转换为 yaml 格式。 |

## demo
```yaml
apiVersion: aam.globalsphare.com/v1alpha1
kind: Trait
metadata:
  name: expose
spec:
  parameter:
    $id: 'https://example.com/person.schema.json'
    $schema: 'https://json-schema.org/draft/2020-12/schema'
    title: expose
    type: object
    properties:
      port:
        type: int
        description: The port to expose
```