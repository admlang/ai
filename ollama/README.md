# adm.ai.ollama

An Ollama client in ADM over `std.net.http`: the native API of a local or remote Ollama
server. Its conversations are `std.ai.llm`'s, so tools, `@tool` methods, MCP tools, pictures
and documents work as they do with a model loaded by `std.ai.llm` itself.

```bash
adm get admlang:adm.ai.ollama
```

```adm
use adm.ai.ollama

let ai = new ollama.Client(model = "qwen3:4b")   // the server of OLLAMA_HOST, else 127.0.0.1:11434
try ai.pull("qwen3:4b")

let chat = ai.chat()
chat.system = "Answer in one sentence."
chat.useTool(new Weather())
print(try chat.say("Do I need a coat in Oslo today?"))

chat.model = "qwen3:30b"                         // the conversation goes on with another model
try chat.ask("Why?")
for {
	let piece = try chat.next()
	break when piece is none
	print(piece)
}
print(chat.usage().inputTokens)

let vectors = try ai.embedder("nomic-embed-text").embed(string["a cat", "a dog"])

for let m in try ai.models() {
	print("{m.name} {m.size}")
}
```

A conversation is a `llm.Chat` and an embedder a `std.ai.embeddings.Embedder`, so code written
for them runs on Ollama, on the hosted providers' libraries and on models loaded by `std.ai`
alike. `Client` also has `generate` (completion without chat markup), `running`, `show`,
`push`, `copy`, `delete`, `create` (from a model or from GGUF and safetensors files on this
machine, uploaded as needed) and `version`; `keepAlive`, `contextSize`, `minP`,
`repeatPenalty` and `token` (for a server behind a proxy) are settings of the client.

## Tests

`adm test ai/ollama` runs against a loopback server. `ADM_TEST_OLLAMA=host:port` adds calls to
a running Ollama with qwen3:0.6b and all-minilm, pulled when missing.
