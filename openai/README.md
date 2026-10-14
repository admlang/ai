# adm.ai.openai

An OpenAI API client in ADM over `std.net.http`. It also talks to every server that speaks the
same API: OpenRouter, Groq, Mistral, DeepSeek, Together, vLLM, LM Studio and Ollama's
compatible endpoint, through `baseURL`.

**Not built yet.** The library is declared so that its name and scope are fixed; this README is
the design the code will follow.

```bash
adm get admlang:adm.ai.openai
```

```adm
use adm.ai.openai

let ai = new openai.Client(apiKey)              // Secret<string>; OPENAI_API_KEY when left out
let reply = try ai.chat("gpt-5-mini", [llm.Message.user("Name three prime numbers")])
print(reply.text)

let stream = try ai.chatStream("gpt-5-mini", messages)
for let piece in stream {
	print(piece.text)
}

let vectors = try ai.embed("text-embedding-3-small", ["one", "two"])   // float[][]
```

## Scope

- Responses API and Chat Completions: messages, system and developer prompts, tools with
  typed arguments, structured output from a struct's schema, images and audio in, reasoning
  effort, seeds, stop sequences, logprobs; streaming through `std.net.http` server-sent events.
- Embeddings, with dimensions.
- Images: generate, edit, variations.
- Audio: transcription and translation from `std.formats.audio` samples, speech synthesis to
  them.
- Files, vector stores and batches.
- Models: list and retrieve.
- `baseURL`, organization and project headers, API version pinning, a custom `http.Client`
  for proxies and timeouts.

## Builds on

`std.net.http` (client, server-sent events), `std.data.encoding` (JSON), `std.ai.llm`
(`Message`, `Role`, tool declarations), `std.formats.audio` and `std.formats.image` for
media in and out.

## Tests

`adm test ai/openai` runs against a loopback server. `ADM_TEST_OPENAI_KEY` adds calls to the
real API with the smallest models; `ADM_TEST_OPENAI_URL` points the same tests at a compatible
server.
