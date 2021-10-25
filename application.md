[TOC]

# Applicaton
由若干 workload 组成，并提供资源以供其他应用依赖（访问）。
 

# Attributes
## 顶级字段
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| apiVersion | string | Y | | 值必须为 aam.globalsphare.com/v1alpha1，命名规范参照 [group/version](https://xuejipeng.github.io/kubebuilder-doc-cn/cronjob-tutorial/gvks.html) 。 |
| kind | string | Y | | 值必须为 Application，命名规范参照 [kind](https://xuejipeng.github.io/kubebuilder-doc-cn/cronjob-tutorial/gvks.html) 。 |
| metadata | [metadata](#metadata) | Y | | 应用的元信息，比如 name 等。 |
| spec| [spec](#spec) | Y | | 应用详细部署及描述信息 |

### metadata
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| name | string | Y | | 应用名 |
| version | string | Y | | 版本 |
| description | string | N | | 应用描述 |
| keywords | string[] | N | | 关键词 |
| author | string | Y | | 作者 |
| maintainers | [maintainer](#maintainers)[] | N | | 维护者列表 |
| repositories | string[] | N | | 代码仓库 |
| bugs | string | N | | bug 反馈 |
| licenses | [license](#license)[] | Y | | 许可 |
| labels | `map[string]string` | N | |  |
| annotations | map[string]string| N | |  |

#### maintainer
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| email | string | Y | | 邮箱 |
| name | string | Y | | 维护者名字 |
| web | string | N | | 博客地址 |

#### license
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| type | string | Y | | license type |
| url | string | Y | | license url |

### spec
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| workloads |[workload](#workload)[]| Y | | 工作负载列表 |
| exports |[resource name](#resource-name)[[operation](#operation)[]]| N | | 可提供其他应用依赖或访问的资源 |
| dependencies |[dependency](#dependency)[]| N | | 依赖的资源（其他应用） |
| userconfigs |[JSONSchema](#JSONSchema)| N | | 运行时动态配置的字段描述 |


#### workload
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| name | string | Y | |工作负载名，当前应用内不可重复。|
| type | string | Y | |工作负载类型，值为 workloadtype name|
| properties |map| Y | |该类型 workload model 提供的属性字段|
| traits | [trait](#Trait)[] | N | | |

##### Trait
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| `type`| string | N | |  A reference to the name of trait definition. For one type of trait, there could be only one configuration in one workload. |
| `properties`| [`Properties`](#properties) | N | |  The properties values to use this trait. |


##### workload demo
```yaml
name: workload-name-1
type: workload-model-name-1
properties:
    image: harbor1.zlibs.com/foo/foo1:v1
    port: 8080
    cmd: [ "/bin/sleep", "3650d" ]
    cpu: "100m"
```

#### exports
<a name="resource-name"></a>
##### resource name
资源名，应用自定义，资源被访问时的 path 部分，比如资源 url：`https://foo.com/rs1` `resourcename` 为 `/rs1`。

##### operation
资源可操作类型：create、update、read、delete。

#### dependency
| Attribute | Type | Required | Default Value | Description |
|-----------|------|----------|---------------|-------------|
| name | string | Y | |依赖的目标应用名|
| version | string | Y | |依赖的目标版本[语义化版本 2.0](https://semver.org/lang/zh-CN/) |
| location | string | N |  | user-defined：需要用户选择已经部署好的实例；<br> https://gitlab.com：用户无法选择, 只能授权使用该网址提供的服务. 针对的是无法私有化部署的软件；<br>user-defined(https://gitlab.com)：用户可以选择已经部署好的实例 或者 用户选择括号中的网站提供给的服务. 针对的是即可私有化部署 有可提供公有服务的软件。|
| uses |[resourcename](#resourcename)[[operation](#operation)[]]| Y | |具体依赖该应用的哪些资源|

#### JSONSchema
运行时动态配置的字段描述规范：[JSON Schema](https://json-schema.org/)

# Full demo
```yaml
apiVersion: aam.globalsphare.com/v1alpha1
kind: Application
metadata:
  name: demo-app-name-1
  version: v0.0.1
  description: 样例应用
  keywords:
    - 样例应用
  author: zhangsan@huanqiu.com
  maintainers:
    - name: zhangsan
      email: foo@gmail.com
      web: https://blog.com
  repositories: [ ]
  bugs: https://bugs.demo-app-name-1.com
  licenses:
    - type: LGPL
      url: https://license.spec.com
spec:
  workloads:
    - name: workload-name-1
      type: workload-model-name-1
      properties:
        image: foo.com/foo/foo1:0.0.1
        port: 8080
        cmd: [ "/bin/sleep", "3650d" ]
        cpu: "100m"
      traits:
        - type: ingress
          properties: 
            k1: v1
            k2: v2
    - name: workload-name-2
      type: workload-model-name-2
      properties:
        image: foo.com/foo/foo2:0.0.1
        port: 8080
        cmd: [ "/bin/sleep", "3650d" ]
        cpu: "100m"
  exports:
    /resource1:
      - create
      - read
      - update
    /resource2:
      - create
      - delete
  dependencies:
    - name: demo-app1
      version: ">=0.0.2"
      location: user-defined(https://www.foo.com)
      uses:
        /resource1:
          - create
          - read
          - update
        /resource2:
          - create
          - delete
  userconfigs:
    "$schema": http://json-schema.org/draft-07/schema#
    "$id": http://example.com/product.schema.json
    title: Product
    description: A product from Acme's catalog
    type: object
    properties:
      param1:
        type: integer
      param2:
        type: string
      param3:
        type: object
        properties:
          param3_1:
            type: number
          param3_2:
            type: number
        required:
          - param3_1
      param4:
        type: array
        items:
          type: string
        minItems: 1
        uniqueItems: true
    required:
      - param2
```


