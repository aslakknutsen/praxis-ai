# Praxis AI — Inference Flow Visualizer

Interactive FlowStory diagram of the Praxis-native inference
path: one in-process filter pipeline instead of Envoy +
Kuadrant Wasm + IPP/EPP ext_proc.

Open [index.html](index.html) via a local static server
(ES modules require HTTP):

```console
cd docs/architecture/praxis-ai-flow
python3 -m http.server 9000
```

Then visit <http://localhost:9000/>.

## Flows

| Flow | What it shows |
| --- | --- |
| External Model (Anthropic) | Auth, rate limit, guardrails, model routing, API translation, credential injection, external provider |
| Internal Model (llm-d) | Same front half, then `endpoint_picker` (GIE semantics) into InferencePool P/D |
| Auth Failure (401) | MaaS key rejection |
| Rate Limit (429) | Token quota exceeded |

## Layout

- **Praxis AI Proxy** — request and response filter stacks (no ext_proc)
- **MaaS API** — key validation callback from `auth`
- **External providers** — Anthropic, OpenAI, Azure, Bedrock, Vertex
- **InferencePool** — llm-d sidecar / prefill / decode / NIXL

Filters marked **COMING SOON** are not yet in-tree
(`auth` as a dedicated MaaS filter, `base_model_to_header`,
`endpoint_picker`).

## Vendor

`vendor/` contains [FlowStory](https://github.com/noyitz/flowstory)
(`flowstory.min.js`, `style.css`), Apache-2.0.
