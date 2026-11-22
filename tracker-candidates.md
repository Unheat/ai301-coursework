# Curated Issues from CodePath Selection Tracker

Candidate issues filtered from the tracker, removing obvious non-code issues (umbrella tickets, translations, bot reports).

## 1. Applied AI & LLM Tooling (Python)
- **vLLM Swagger Body Bug**: https://github.com/vllm-project/production-stack/issues/667
  - *Title:* `bug: Can't pass body of request in swagger docs`
  - *Language:* Python (FastAPI / Swagger docs)
- **vLLM Whisper Audio Endpoint**: https://github.com/vllm-project/production-stack/issues/410
  - *Title:* `feature: Transcription API (/v1/audio/transcriptions) for whisper`
  - *Language:* Python (FastAPI endpoint)
- **ExecuTorch Model State Serialization**: https://github.com/pytorch/executorch/issues/8542
  - *Title:* `Python apis for loading and saving .ptd from dictionaries`
  - *Language:* Python (Meta edge AI runtime)
- **PyTorch Safetensors Model Export**: https://github.com/pytorch/pytorch/issues/153410
  - *Title:* `[Feature request] torch.export .save/.load could support safetensors and/or weights_only=True`
  - *Language:* Python (PyTorch 2.0 export)
- **PyTorch Graph Breaks Summary Tool**: https://github.com/pytorch/pytorch/issues/153669
  - *Title:* `Make tlparse able to show a summary of distinct graph breaks`
  - *Language:* Python (CLI tooling)
- **OpenTelemetry Ollama Spans**: https://github.com/open-telemetry/opentelemetry-ebpf-instrumentation/issues/3866
  - *Title:* `Extend Ollama to match on embedding spans`
  - *Language:* C / Go (Ollama LLM observability)

## 2. Spotify Backstage (TypeScript / Full-Stack)
- **Tech Radar Quadrant Bug**: https://github.com/backstage/community-plugins/issues/10802
  - *Title:* `🐛 tech-radar: Error if not all quadrants are set`
  - *Language:* TypeScript / React
- **GraphiQL Endpoints Bug**: https://github.com/backstage/community-plugins/issues/10998
  - *Title:* `🐛 graphiql: The /graphiql page always shows "No endpoints available"`
  - *Language:* TypeScript / React
- **Grafana State Column Bug**: https://github.com/backstage/community-plugins/issues/10991
  - *Title:* `🐛 Grafana: Alert Card Do no show State Column in New FrontEnd System`
  - *Language:* TypeScript / React

## 3. Python Backend & Build Systems
- **OpenSearch os.chdir Refactor**: https://github.com/opensearch-project/opensearch-build/issues/775
  - *Title:* `Remove all os.chdir in favor of cwd/working directory in execute`
  - *Language:* Python
- **OpenSearch ruamel.yaml Migration**: https://github.com/opensearch-project/opensearch-build/issues/2614
  - *Title:* `Migrate the python workflows yaml module to use ruamel.yaml`
  - *Language:* Python

## 4. OpenSearch Dashboards (TypeScript / Search UI)
- **Deprecated uuid Dependency**: https://github.com/opensearch-project/OpenSearch-Dashboards/issues/7141
  - *Title:* `[BUG] The uuid dependency is on a deprecated version of uuid (3.3.2)`
  - *Language:* TypeScript
- **Comma Separated Filter Bug**: https://github.com/opensearch-project/OpenSearch-Dashboards/issues/6670
  - *Title:* `[BUG] Comma Seperated Filter Values`
  - *Language:* TypeScript

## 5. Systems / Rust & Go Tooling
- **Weaver Semver Crate Migration**: https://github.com/open-telemetry/weaver/issues/579
  - *Title:* `Replace the regex in from_semconv_specs with the semver crate`
  - *Language:* Rust
- **Collector Contrib CPU Attributes**: https://github.com/open-telemetry/opentelemetry-collector-contrib/issues/39333
  - *Title:* `Add system.cpu.socket.id and system.cpu.core.id attributes`
  - *Language:* Go
