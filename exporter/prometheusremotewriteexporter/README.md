# Prometheus Remote Write Exporter

| Status                   |                   |
| ------------------------ |-------------------|
| Stability                | [beta]            |
| Supported pipeline types | metrics           |
| Distributions            | [core], [contrib] |

Prometheus Remote Write Exporter sends OpenTelemetry metrics
to Prometheus [remote write compatible
backends](https://prometheus.io/docs/operating/integrations/)
such as Cortex and Thanos.
By default, this exporter requires TLS and offers queued retry capabilities.

:warning: Non-cumulative monotonic, histogram, and summary OTLP metrics are
dropped by this exporter.

A [design doc](DESIGN.md) is available to document in detail
how this exporter works.

## Getting Started

The following settings are required:

- `endpoint` (no default): The remote write URL to send remote write samples.

By default, TLS is enabled and must be configured under `tls:`:

- `insecure` (default = `false`): whether to enable client transport security for
  the exporter's connection.

As a result, the following parameters are also required under `tls:`:

- `cert_file` (no default): path to the TLS cert to use for TLS required connections. Should
  only be used if `insecure` is set to false.
- `key_file` (no default): path to the TLS key to use for TLS required connections. Should
  only be used if `insecure` is set to false.

The following settings can be optionally configured:

- `external_labels`: map of labels names and values to be attached to each metric data point
- `headers`: additional headers attached to each HTTP request.
  - *Note the following headers cannot be changed: `Content-Encoding`, `Content-Type`, `X-Prometheus-Remote-Write-Version`, and `User-Agent`.*
- `namespace`: prefix attached to each exported metric name.
- `remote_write_queue`: fine tuning for queueing and sending of the outgoing remote writes.
  - `enabled`: enable the sending queue
  - `queue_size`: number of OTLP metrics that can be queued. Ignored if `enabled` is `false`
  - `num_consumers`: minimum number of workers to use to fan out the outgoing requests.

Example:

```yaml
exporters:
  prometheusremotewrite:
    endpoint: "https://my-cortex:7900/api/v1/push"
    wal: # Enabling the Write-Ahead-Log for the exporter.
      directory: ./prom_rw # The directory to store the WAL in
      buffer_size: 100 # Optional count of elements to be read from the WAL before truncating; default of 300
      truncate_frequency: 45s # Optional frequency for how often the WAL should be truncated. It is a time.ParseDuration; default of 1m
```

Example:

```yaml
exporters:
  prometheusremotewrite:
    endpoint: "https://my-cortex:7900/api/v1/push"
    external_labels:
      label_name1: label_value1
      label_name2: label_value2
```

## Advanced Configuration

Several helper files are leveraged to provide additional capabilities automatically:

- [HTTP settings](https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/confighttp/README.md)
- [TLS and mTLS settings](https://github.com/open-telemetry/opentelemetry-collector/blob/main/config/configtls/README.md)
- [Retry and timeout settings](https://github.com/open-telemetry/opentelemetry-collector/blob/main/exporter/exporterhelper/README.md), note that the exporter doesn't support `sending_queue` but provides `remote_write_queue`.

## Metric names and labels normalization

OpenTelemetry metric names and attributes are normalized to be compliant with Prometheus naming rules. [Details on this normalization process are described in the Prometheus translator module](../../pkg/translator/prometheus/).

[beta]:https://github.com/open-telemetry/opentelemetry-collector#beta
[contrib]:https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol-contrib
[core]:https://github.com/open-telemetry/opentelemetry-collector-releases/tree/main/distributions/otelcol
