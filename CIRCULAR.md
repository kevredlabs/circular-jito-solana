# Circular Fast on Jito BAM

This fork exports BAM-verified transactions to **Circular Fast** (ARB stream) without blocking the validator leader path.

## Architecture

```text
BAM Node → BamReceiveAndBuffer (sigverify)
              │
              ├─ try_send(Arc) → queue → circExporter → gRPC SendTransaction → Fast
              └─ parse / BamScheduler / execute
```

- **BAM hot path:** `Arc` share + non-blocking `try_send` into a bounded queue.
- **`circExporter` thread:** vote filtering, wire-byte copy, unary gRPC `SendTransaction` (`forward=false`).
- Slow or dead Fast → counted drops only; no backpressure on BAM / banking.

## Docs

- Grafana dashboard & metric field guide: [`grafana/README.md`](grafana/README.md)
- Dashboard JSON: [`grafana/circular-bam-exporter.json`](grafana/circular-bam-exporter.json)
