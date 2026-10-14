# ADM AI provider clients

Official [ADM](https://github.com/admlang/adm) libraries that call hosted model APIs. `std.ai`
runs models on the machine; these libraries talk to a provider over `std.net.http`, in ADM,
with no SDK to install. Each is published as `adm.ai.<provider>` and signed by the `admlang`
publisher. They live outside the standard library because provider APIs change every few
weeks, and a new model or endpoint should ship without a std release.

| Library | Provider | What it covers |
|---|---|---|
| [`adm.ai.openai`](openai/) | OpenAI, and every server that speaks its API (OpenRouter, Groq, Mistral, DeepSeek, Together, vLLM, LM Studio) | responses and chat completions with tools and streaming, embeddings, images, audio, files, batches |
| [`adm.ai.anthropic`](anthropic/) | Anthropic | the Messages API: tool use, streaming, extended thinking, prompt caching, token counting, files, batches |
| [`adm.ai.gemini`](gemini/) | Google (Gemini API) | generateContent and streaming, function calling, embeddings, files, context caching, grounding |
| [`adm.ai.ollama`](ollama/) | Ollama | chat and generate with streaming, embeddings, model management |

The hosted models of the clouds (Amazon Bedrock, Vertex AI, Azure OpenAI) are modules of the
cloud libraries, since they take the cloud's credentials: `adm.cloud.aws.bedrock`,
`adm.cloud.gcp.vertex`, `adm.cloud.azure.openai`.

## One shape

The clients share one shape, so a program can move between a provider and a local model:

- a `Client` made from an API key (a `Secret<string>`, or the provider's usual environment
  variable when none is given) and an optional base URL;
- `chat` takes a prompt or a list of `std.ai.llm` messages, and tool declarations and returns a reply, or streams it
  as the tokens arrive; tool calls come back typed and the answers go in as messages;
- `embed` returns vectors as `float[]`;
- every failure is one error type per library with a `kind` (`Auth`, `RateLimited`,
  `Overloaded`, `BadRequest`, `NotFound`, `ContentFiltered`, `Transport`, `Server`), the
  provider's own code and message, the request id, and the pause a `Retry-After` asked for.
  Rate-limited and overloaded calls are retried with backoff up to a limit the client sets;
- usage (input, output and cached tokens) comes with every reply.

What is specific to a provider (reasoning effort, prompt caching, grounding, safety settings)
is a typed option of that library, never a string the program has to know.

## Tests

Each suite runs against a loopback server that answers as the provider does, so `adm test`
needs no account. With a key the suite also calls the real API, with the smallest models:

```bash
ADM_TEST_OPENAI_KEY=sk-... adm test ai/openai
ADM_TEST_ANTHROPIC_KEY=sk-ant-... adm test ai/anthropic
ADM_TEST_GEMINI_KEY=... adm test ai/gemini
ADM_TEST_OLLAMA=127.0.0.1:11434 adm test ai/ollama
```
