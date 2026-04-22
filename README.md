# aquascape-proto

Single source of truth for gRPC contracts across the aquascape-studio ecosystem.

Every service repo (`aquascape-api` in Rust, `aquascape-sim` in Python,
`aquascape-web` and `aquascape-mobile` via Connect-ES) consumes this repo as a
git submodule and runs `buf generate` in its own CI to produce language-native
bindings. Nothing in this repo compiles on its own — it's contract-only.

## Layout

```
proto/aquascape/v1/
├── common.proto   # shared scalars: ids, units, errors
├── botany.proto   # plant species + cultivation envelopes
├── scape.proto    # tank + hardscape + plant placement
├── sim.proto      # growth simulation service (Python backend)
└── api.proto      # public API service (Rust backend, web/mobile call this)
```

## Workflow

Install [buf](https://buf.build/docs/installation) and run:

```bash
buf lint
buf breaking --against '.git#branch=main'
buf format -w
```

CI runs all three on every PR. This repo ships only .proto files; every
consumer generates its own language bindings via its own buf.gen.yaml.

## Versioning

Breaking wire changes require a new `vN+1` package. Non-breaking additions
(new fields with fresh tags, new messages, new RPC methods) land on `main`.
Consumers pin via submodule SHA.
