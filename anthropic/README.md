# adm.ai.anthropic

An Anthropic API client in ADM over `std.net.http`: the Messages API for Claude models.

```bash
adm get admlang:adm.ai.anthropic
```

```adm
use adm.ai.anthropic

let ai = new anthropic.Client(apiKey)           // ANTHROPIC_API_KEY when left out
let reply = try ai.chat("claude-sonnet-5-5", "Summarise this: {text}", system = "Be brief")
print(reply.text)
print(reply.usage.inputTokens)

let stream = try ai.chatStream("claude-sonnet-5-5", messages, tools = [weather])
for let event in stream {
	print(event.text) when event.kind == anthropic.EventKind.Text
}
```

## Tests

`adm test ai/anthropic` runs against a loopback server. `ADM_TEST_ANTHROPIC_KEY` adds calls to
the real API with the smallest model.
