# Prometheus Chrony Exporter

[![Build Status](https://circleci.com/gh/SuperQ/chrony_exporter/tree/main.svg?style=svg)](https://circleci.com/gh/SuperQ/chrony_exporter/tree/main)

This is a [Prometheus Exporter](https://prometheus.io) for [Chrony NTP](https://chrony.tuxfamily.org/).

## Installation

For most use-cases, simply download the [the latest
release](https://github.com/superq/chrony_exporter/releases).

### Building from source

You need a Go development environment. Then, simply run `make` to build the
executable:

    make

This uses the common prometheus tooling to build and run some tests.

### Building a Docker container

You can build a Docker container with the included `docker` make target:

    make promu
    promu crossbuild -p linux/amd64 -p linux/arm64
    make docker

This will not even require Go tooling on the host.

## Running

A minimal invocation looks like this:

    ./chrony_exporter

Supported parameters include:

- `--web.listen-address`: the address/port to listen on (default: `":9290"`)
- `--collector.sources`: Enable/disable the collection of `chronyc sources` metrics. (Default: Disabled)
- `--collector.tracking`: Enable/disable the collection of `chronyc tracking` metrics. (Default: Enabled)

To disable a collector, use `--no-`. (i.e. `--no-collector.tracking`)

By default, the exporter will bind on `:9123`.

## Prometheus Rules

You can use [Prometheus rules](https://prometheus.io/docs/prometheus/latest/configuration/recording_rules/) to pre-compute some values.

For example, the maximum clock error can be computed from several metrics as [documented in the Chrony man pages](https://chrony.tuxfamily.org/doc/4.3/chronyc.html).

```yaml
groups:
  - name: Chrony
    rules:
      - record: instance:chrony_clock_error_seconds:abs
        expr: >
          abs(chrony_tracking_last_offset_seconds)
          +
          chrony_tracking_root_dispersion_seconds
          +
          (0.5 * chrony_tracking_root_delay_seconds)
```

## TLS and basic authentication

The Chrony Exporter supports TLS and basic authentication.

To use TLS and/or basic authentication, you need to pass a configuration file
using the `--web.config.file` parameter. The format of the file is described
[in the exporter-toolkit repository](https://github.com/prometheus/exporter-toolkit/blob/master/docs/web-configuration.md).
