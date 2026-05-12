# Next.js MVP Fallback API Pattern for 9Router

Use when a Next.js SaaS MVP needs AI-backed actions immediately, before full backend/AI infrastructure is ready.

## Goal

Expose server-side actions that try 9Router/OpenAI-compatible chat first, but always return deterministic useful demo output if env/config/API fails. This keeps UI demos clickable and prevents missing AI credentials from breaking the product.

## Env aliases

Prefer server-only env vars:

```env
NINEROUTER_URL=https://9router.phamphunguyenhung.com/v1
NINEROUTER_KEY=...
NINEROUTER_MODEL=cx/gpt-5.5
```

Also support aliases if repo already uses them:

```txt
NINEROUTER_REMOTE_URL
AI_GATEWAY_URL
OPENAI_BASE_URL
OPENAI_API_KEY
AI_GATEWAY_API_KEY
```

Never expose keys client-side.

## Helper shape

```ts
type JsonRecord = Record<string, unknown>;

const DEFAULT_MODEL = "cx/gpt-5.5";

function getConfig() {
  const baseUrl = (process.env.NINEROUTER_URL || process.env.NINEROUTER_REMOTE_URL || process.env.OPENAI_BASE_URL || "").replace(/\/$/, "");
  const key = process.env.NINEROUTER_KEY || process.env.OPENAI_API_KEY || "";
  const model = process.env.NINEROUTER_MODEL || DEFAULT_MODEL;
  return { baseUrl, key, model };
}

export async function runNinerouterJsonTask(
  system: string,
  input: JsonRecord,
  fallback: JsonRecord,
): Promise<JsonRecord> {
  const { baseUrl, key, model } = getConfig();
  if (!baseUrl || !key) return fallback;

  try {
    const res = await fetch(`${baseUrl}/chat/completions`, {
      method: "POST",
      headers: { "Content-Type": "application/json", Authorization: `Bearer ${key}` },
      body: JSON.stringify({
        model,
        temperature: 0.2,
        response_format: { type: "json_object" },
        messages: [
          { role: "system", content: system },
          { role: "user", content: JSON.stringify(input) },
        ],
      }),
    });
    if (!res.ok) return fallback;
    const json = await res.json();
    const content = json?.choices?.[0]?.message?.content;
    return typeof content === "string" ? JSON.parse(content) : fallback;
  } catch {
    return fallback;
  }
}
```

## Action wiring rules

- Keep handler async all the way up: if `buildActionResult()` becomes async, `await` it in API handler before idempotency/result wrapping.
- Give model strict JSON shape in system prompt and still cast/check fallback safely.
- Good MVP flows: document check, trade-document generation, buyer match/outreach.
- Missing key/API failure is not user-visible failure for demo; return fallback and optionally annotate source/confidence.

## UX rule

Backend fallback is only half of MVP. The frontend button must show feedback: loading/done/toast/result panel. A server action wired to a dead-looking button still feels broken.
