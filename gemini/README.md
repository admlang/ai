# adm.ai.gemini

A Gemini API client in ADM over `std.net.http`, for Google's models through an API key. The
same models under Google Cloud credentials are `adm.cloud.gcp.vertex`.

**Not built yet.** The library is declared so that its name and scope are fixed; this README is
the design the code will follow.

```bash
adm get admlang:adm.ai.gemini
```

```adm
use adm.ai.gemini

let ai = new gemini.Client(apiKey)              // GEMINI_API_KEY when left out
let reply = try ai.chat("gemini-2.5-flash", [llm.Message.user("What is in this picture?", picture)])
print(reply.text)

let vectors = try ai.embed("gemini-embedding-001", ["one", "two"])
```

## Scope

- generateContent and its streaming form: system instructions, multi-turn chats, function
  calling with typed arguments, structured output from a struct's schema, images, audio,
  video and documents in, safety settings, thinking budgets.
- Embeddings with task types and dimensions.
- Files: upload, poll, use in prompts.
- Context caching: create, use, extend, delete.
- Grounding with Google Search and URL context, code execution, with the citations typed.
- Models: list and retrieve, token counting.
- `baseURL` and API version pinning, a custom `http.Client`.

## Builds on

`std.net.http` (client, server-sent events), `std.data.encoding` (JSON), `std.ai.llm`
(`Message`, `Role`, tool declarations), `std.formats.image`, `std.formats.audio` and
`std.formats.video` for media in.

## Tests

`adm test ai/gemini` runs against a loopback server. `ADM_TEST_GEMINI_KEY` adds calls to the
real API with the smallest model.
