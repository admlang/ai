# adm.ai.anthropic

An Anthropic API client in ADM over `std.net.http`: the Messages API for Claude models.

**Not built yet.** The library is declared so that its name and scope are fixed; this README is
the design the code will follow.

```bash
adm get admlang:adm.ai.anthropic
```

```adm
use adm.ai.anthropic

let ai = new anthropic.Client(apiKey)           // ANTHROPIC_API_KEY when left out
let reply = try ai.chat("claude-sonnet-5-5", [llm.Message.user("Summarise this")], system = "Be brief")
print(reply.text)
print(reply.usage.inputTokens)

let stream = try ai.chatStream("claude-sonnet-5-5", messages, tools = [weather])
for let event in stream {
	print(event.text) when event.kind == anthropic.EventKind.Text
}
```

## Scope

- Messages: system prompts, multi-turn conversations, tool use with typed arguments and
  results, images and documents in, stop sequences, temperature and sampling, metadata.
- Streaming with every event kind (text, tool input deltas, thinking, usage).
- Extended thinking with a budget, and the thinking blocks kept across turns.
- Prompt caching: cache breakpoints on system prompts, tools and messages; cached token
  counts in usage.
- Token counting before a call.
- Files and message batches (create, poll, results).
- Models: list and retrieve.
- `baseURL` and API version pinning, a custom `http.Client` for proxies and timeouts.

## Builds on

`std.net.http` (client, server-sent events), `std.data.encoding` (JSON), `std.ai.llm`
(`Message`, `Role`, tool declarations), `std.formats.image` and `std.formats.docs` for
content in.

## Tests

`adm test ai/anthropic` runs against a loopback server. `ADM_TEST_ANTHROPIC_KEY` adds calls to
the real API with the smallest model.
