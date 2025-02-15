# Resource Processor

| Status                   |                       |
| ------------------------ | --------------------- |
| Stability                | [beta]                |
| Supported pipeline types | traces, metrics, logs |
| Distributions            | [core], [contrib]     |

The resource processor can be used to apply changes on resource attributes.
Please refer to [config.go](./config.go) for the config spec.

`attributes` represents actions that can be applied on resource attributes.
See processor/attributesprocessor/README.md for more details on supported attributes actions.

Examples:

```yaml
processors:
  resource:
    attributes:
    - key: cloud.availability_zone
      value: "zone-1"
      action: upsert
    - key: k8s.cluster.name
      from_attribute: k8s-cluster
      action: insert
    - key: redundant-attribute
      action: delete
```

Refer to [config.yaml](./testdata/config.yaml) for detailed
examples on using the processor.

[beta]:https://github.com/open-telemetry/opentelemetry-collector#beta
[contrib]:https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
[core]:https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol
