[TOC]

# WorkloadType

The role of a `WorkloadType` entity is to permit workload providers to declare, in infrastructure-neutral format, the runtime characteristics of such unit of execution. 

For example, each microservice in an application is described as a workload. Note that `WorkloadType` itself is *NOT* an instance of that microservice, but a declaration of the configurable attributes of that microservice. These configurable attributes should be expose as a list of parameters 
which allow the application team to set and instantiate this workload later at deployment time.

In practice, a simple containerized workload, a Helm chart, or a cloud database may all be modeled as a workload.

## Top-Level Attributes

Here are the attributes that provide top-level information about the workload definition.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `apiVersion` | `string` | Y | | A string that identifies the version of the schema the object should have. The core types uses `aam.globalsphare.com/v1alpha1` in this version of model |
| `kind` | `string` | Y || Must be `WorkloadType` |
| `metadata` | [`Metadata`](#metadata) | Y | | Entity metadata. |
| `spec`| [`Spec`](#spec) | Y | | The specification for the workload definition. |

### Metadata

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
| `traits` | [trait](#trait.md)[] | N | | 可以支持的 trait 列表，名字即可。 |

###### Example

Below is a full example of CUE based workload definition named `webserver`:

```yaml
apiVersion: aam.globalsphare.com/v1alpha1
kind: WorkloadType
metadata:
  name: webserver
spec:
    parameter:
      $id: 'https://example.com/person.schema.json'
      $schema: 'https://json-schema.org/draft/2020-12/schema'
      title: Person
      type: object
      properties:
        firstName:
          type: string
          description: The person's first name.
        lastName:
          type: string
          description: The person's last name.
        age:
          description: Age in years which must be equal to or greater than zero.
          type: integer
          minimum: 0
    traits:
      - trait1
      - trait2
```

With above `webserver` installed in the platform, user would be able to deploy this workload in an application as below:

```yaml
apiVersion: aam.globalsphare.com/v1alpha1
kind: Application
metadata:
  name: webserver-demo
spec:
  workloads:
    - name: hello-world
      type: webserver               # claim to deploy webserver workload definition
      properties:                   # setting parameter values
        image: crccheck/hello-world
        port: 8000                  # this port will be automatically exposed to public
      traits:
        trait1:
          properties:
            parameter1: 1
            parameter2: 2

```