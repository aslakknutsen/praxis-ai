# Praxis AI — Inference Flow Visualizer

Interactive FlowStory diagram of a **single HTTP listener**
that dispatches specialist stacks with Praxis
`branch_chains` (not a linear mega-pipeline, and not
path-selected named `filter_chains`).

Open [index.html](index.html) via a local static server
(ES modules require HTTP):

```console
cd docs/architecture/praxis-ai-flow
python3 -m http.server 9000
```

Then visit <http://localhost:9000/>.

## Model

```text
security trunk → path_classify (filter_results.api)
                      │
        ┌─────────────┼─────────────┐
        ▼             ▼             ▼
   responses      chat         inference
   (full-flow)    (chat)       (pool prep)
        │             │             │
        └─────────────┴─────────────┘
                      │
              rejoin: next → shared egress
              credential_injection → endpoint_picker
              → router / LB → upstreams
```

`path_classify` is the path→result emitter required for
real `branch_chains` (`on_result`). Branches cannot match
URI natively. Marked **COMING SOON** in the diagram.

## Flows

| Flow | What it shows |
| --- | --- |
| Responses API (`/v1/responses`) | Security → classify → responses arm (full-flow filters) → egress → provider |
| Chat Completions (`/v1/chat`) | Security → classify → chat arm → egress → Anthropic |
| Inference pool (`/v1/inference`) | Security → classify → inference arm → `endpoint_picker` → llm-d P/D |
| Auth Failure (401) | Stops in security trunk |
| Rate Limit (429) | Stops in security trunk |

Inactive branch arms stay on the canvas but are not
lit for the active flow.

## Coming soon (diagram badges)

- `path_classify` (path → `filter_results.api`)
- `auth` (MaaS-backed)
- `base_model_to_header`
- `endpoint_picker` (GIE semantics, no ext_proc)

## Vendor

`vendor/` contains [FlowStory](https://github.com/noyitz/flowstory)
(`flowstory.min.js`, `style.css`), Apache-2.0.
