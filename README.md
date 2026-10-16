# Seedance 2.0 Fast API (`doubao-seedance-2.0-fast`)

<!-- conv-kit:v1 -->

<p align="center">
  <img src="assets/badges/price.svg" alt="observed unit price"> <img src="assets/badges/billing.svg" alt="billing model"> <img src="assets/badges/compat.svg" alt="OpenAI-compatible endpoint">
</p>

<p align="center">
  <img src="assets/01-product-ad-ceramic-mug-thumb.jpg" width="820" alt="Sample video frame generated through APIMart">
</p>

> **from $0.0213 per second** on the APIMart route (billed by resolution tier) — un único endpoint compatible con OpenAI en `https://api.apimart.ai/v1`, sin plan mensual. *(observado el 2026-10-12)*

**[Obtener una clave de API](https://go.apimart.ai/k-5b05ea)** · **[Precios en vivo](https://go.apimart.ai/k-3b0112)** · **[Página del modelo](https://go.apimart.ai/k-9d453f)** · [⚡ Inicio rápido en 60 segundos](#quickstart)

**Por qué los equipos usan Seedance 2.0 Fast (`doubao-seedance-2.0-fast`) a través de APIMart**

- **Una clave, todo el catálogo.** La misma base URL `https://api.apimart.ai/v1` y la misma cabecera `Authorization` dan acceso a Seedance 2.0 Fast (`doubao-seedance-2.0-fast`) y a más de 300 modelos de imagen, vídeo y lenguaje: cambia el campo `model`, no tu cliente.
- **Mínimo de $1, pago por uso.** Sin suscripción ni plan prepago que dimensionar por adelantado: recarga desde $1 y úsalo en llamadas.
- **El cargo vuelve en la respuesta.** Cada tarea completada informa del importe facturado (`cost` / `credits_cost`); una tarea marcada como `failed` se reembolsa.
- **Asíncrono por diseño.** Envía, toma el `task_id`, consulta `GET /v1/tasks/{id}`: el batching y los reintentos son trabajo de cola normal, no una integración a medida.

<!-- /conv-kit:v1 -->

**También conocido como:** Dreamina

Seedance 2.0 Fast is ByteDance's faster Seedance 2.0 route on APIMart: text-to-video and image-to-video with the same framing controls, billed per generated second.

## Model id and routes

| Route | `model` value | Billing | Notes |
| --- | --- | --- | --- |
| Text-to-video / image-to-video | `doubao-seedance-2.0-fast` | per generated second, by resolution | see the [model page](https://go.apimart.ai/k-9d453f) for the exact tiers |

Endpoint: `POST https://api.apimart.ai/v1/videos/generations`, then poll `GET /v1/tasks/{task_id}`.

## Pricing

<!-- pricing:model:start -->
| Resolution | List price | Effective price |
| --- | --- | --- |
| per generated second | — | see the live pricing page |


<!-- pricing:model:end -->

Prices are a snapshot; the [pricing page](https://go.apimart.ai/k-3b0112) and [`data/model.json`](data/model.json) are refreshed by CI, and a completed task reports the exact amount in its `cost` field.

## Request parameters

| Field | Type | Default | Notes |
| --- | --- | --- | --- |
| `model` | string | required | the model id shown in the route table above |
| `prompt` | string | required | the shot description; camera and subject motion belong here |
| `duration` | integer | route default | clip length in seconds — billing is per generated second |
| `resolution` | string | route default | 480P / 720P / 1080P or the route’s own tiers |
| `size` / `aspect_ratio` | string | `16:9` | output framing |
| `image_urls` | string[] | — | first frame, first + last frame, or style references, where supported |
| `seed` | integer | — | fix the seed to reproduce a take |

The table lists the fields accepted by the APIMart `videos/generations` endpoint. Check the [model page](https://go.apimart.ai/k-9d453f) for anything this model adds on top.

## Quickstart

```bash
curl --request POST --url https://api.apimart.ai/v1/videos/generations \
  --header "Authorization: Bearer $APIMART_API_KEY" --header 'Content-Type: application/json' \
  -d '{"model":"doubao-seedance-2.0-fast","prompt":"A bamboo forest at sunrise, slow push-in","duration":5,"resolution":"720P","size":"16:9"}'
```

```python
import os, time, requests

BASE = "https://api.apimart.ai/v1"
HEADERS = {"Authorization": f"Bearer {os.environ['APIMART_API_KEY']}", "Content-Type": "application/json"}

created = requests.post(f"{BASE}/videos/generations", headers=HEADERS, timeout=60, json={
    "model": "doubao-seedance-2.0-fast", "prompt": "A bamboo forest at sunrise, slow push-in", "duration": 5,
}).json()
task_id = created["data"]["id"]

while True:
    task = requests.get(f"{BASE}/tasks/{task_id}", headers=HEADERS, timeout=60).json()["data"]
    if task["status"] in ("completed", "failed"):
        break
    time.sleep(5)
print(task.get("cost"), task["result"]["videos"][0]["url"])
```

Runnable versions: [`examples/`](examples). The task lifecycle is `pending → processing → completed | failed`.

## FAQ

**¿Cuál es el id del modelo Seedance 2.0 Fast?**

`doubao-seedance-2.0-fast`. Consulta la tabla de rutas de arriba.

**¿Cómo se factura?**

**from $0.0213 per second** on the APIMart route (billed by resolution tier). La tarea completada informa del importe exacto en su campo `cost`; una tarea fallida se reembolsa.

**How do I get a key?**

Use the [Get an API key](https://go.apimart.ai/k-5b05ea) link — $1 minimum top-up, no plan required.

<!-- related-repos:auto -->
## Related repositories

- **Same model, this language** — the other half of this pair: [seedance-2-0-fast-prompts-es](https://github.com/qaukkgecogrt/seedance-2-0-fast-prompts-es)
- **Same model, other languages** — 9 more language builds: 中文 [seedance-2-0-fast-api-cn](https://github.com/aryvk/seedance-2-0-fast-api-cn) · Deutsch [seedance-2-0-fast-api-de](https://github.com/m7sqz596a/seedance-2-0-fast-api-de) · Français [seedance-2-0-fast-api-fr](https://github.com/j-az78q90oa/seedance-2-0-fast-api-fr) · Relay/gateway [seedance-2-0-fast-api-gateway](https://github.com/nmx58j60dc8c8/seedance-2-0-fast-api-gateway) · Bahasa [seedance-2-0-fast-api-id](https://github.com/ulazbd7hzzt/seedance-2-0-fast-api-id) · 日本語 [seedance-2-0-fast-api-ja](https://github.com/sxb6d8v/seedance-2-0-fast-api-ja) · 한국어 [seedance-2-0-fast-api-ko](https://github.com/k9fc4/seedance-2-0-fast-api-ko) · Português `seedance-2-0-fast-api-pt` · Русский [seedance-2-0-fast-api-ru](https://github.com/wlpm01/seedance-2-0-fast-api-ru)
- **More models in this language** — [midjourney-api-es](https://github.com/xbuu0/midjourney-api-es) · [gemini-2-5-flash-image-preview-api-es](https://github.com/ekw3w/gemini-2-5-flash-image-preview-api-es) · [nano-banana-api-es](https://github.com/gbm5mfl1/nano-banana-api-es) · [gemini-3-1-flash-image-preview-api-es](https://github.com/x3kcczm8l29xn/gemini-3-1-flash-image-preview-api-es) · [nano-banana-2-api-es](https://github.com/d63hy9t-4pp/nano-banana-2-api-es) · [gemini-3-pro-image-preview-api-es](https://github.com/uv9d8/gemini-3-pro-image-preview-api-es)
- **Topic hubs** — every build of this model, all languages: [seedance-2-0-fast](https://github.com/topics/seedance-2-0-fast) · [seedance-2-0-fast-api](https://github.com/topics/seedance-2-0-fast-api)

This repository is one build in a cross-linked set of API guides for the same model family; each language build links back to the others.
<!-- /related-repos:auto -->

## Attributed links (how this repository is measured)

| Purpose | Attributed link | Target |
| --- | --- | --- |
| Model page | <https://go.apimart.ai/k-9d453f> | `apimart.ai/es/model/doubao-seedance-2-0-fast` |
| Live pricing | <https://go.apimart.ai/k-3b0112> | `apimart.ai/es/pricing` |
| Get an API key | <https://go.apimart.ai/k-5b05ea> | `apimart.ai/es/keys` |

Outbound APIMart links are minted through the promo link API; hand-made tracking parameters are rejected by
`tools/check_links.py` in CI.

## Disclosure

Seedance 2.0 Fast is a third-party model served through APIMart; this repository documents how to call it and publishes
model ids and prices, and does not claim official status. Model names, prices and documentation belong to their
respective owners.

## License

MIT — see [LICENSE](LICENSE).
