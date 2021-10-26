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
| `conflictsWith` | `string[]` | N | | A list of traits that would be conflict with this trait when applied to same component model type. For example, `autoscaling` may conflict with `cron-autoscaling`. |
| `schematic` | [Schematic](#schematic) | Y | | Schematic information for this traits. |

##### Schematic

This section declares the schematic of a component that could be instantiated as part of an application in the later deployment workflow. Note that AAM itself has no enforcement on how to implement the schematic as long as it could:
  1. model a deployable unit;
  2. expose a JSON schema or equivalent parameter list. 

In Island, `cue` are supported for now.

## demo
```yaml
apiVersion: aam.globalsphare.com/v1alpha1
kind: Trait
metadata:
  name: expose
spec:
  schematic:
    cue:
      template: |
        parameter: {
          http: [string]: int
        }

        outputs: {
          for k, v in parameter.http {
            "\(k)": {
              apiVersion: "v1"
              kind:       "Service"
              spec: {
                selector:
                  app: context.name
                ports: [{
                  port:       v
                  targetPort: v
                }]
              }
            }
          }
        }
```