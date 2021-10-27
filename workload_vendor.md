[TOC]

# Workloadvendor

## Top-Level Attributes

Here are the attributes that provide top-level information about the workload definition.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `apiVersion` | `string` | Y | | foo.com/v1alpha1 |
| `kind` | `string` | Y || Must be `Workloadvendor` |
| `metadata` | [`Metadata`](#metadata) | Y | | Entity metadata. |
| `spec`| [cue template](#https://cuelang.org/) | Y | | The specification for the workload definition. |

### Metadata

This metadata section is made up of several top-level keys.

Metadata provides information about the contents of the object.

| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `name` | `string` | Y | | vendor name |
| `annotations` | `map[string]string`| N | |  |

## full demo
```
impl: mysql

parameter: {
		certificate: string
		capacity: string
		scale: string
}

construct: {
		apiVersion: v1
		kind: deployment
		metadata:
			name: demo-1
		sepc:
			scale: parameter.scale
			capacity: parameter.capacity

}
HighAvailable: {
		parameter: {
				nodenumber: number
		}
		output: {
				apiVersion: autoscaling/v2beta2
				kind: HorizontalPodAutoscaler
				metadata:
					name: php-apache
				spec:
					scaleTargetRef:
						apiVersion: apps/v1
						kind: Deployment
						name: demo-1
					minReplicas: 1
					maxReplicas: HighAvailable.parameter.nodenumber
		}
}


```