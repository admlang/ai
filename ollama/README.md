# adm.ai.ollama

An Ollama client in ADM over `std.net.http`: the native API of a local or remote Ollama
server. `std.ai.llm` loads GGUF files itself; this library is for programs that let Ollama
hold the models and the GPU.

**Not built yet.** The library is declared so that its name and scope are fixed; this README is
the design the code will follow.

```bash
adm get admlang:adm.ai.ollama
```

```adm
use adm.ai.ollama

let ai = new ollama.Client("http://127.0.0.1:11434")   // OLLAMA_HOST, or this, when left out
try ai.pull("qwen3:4b")
let reply = try ai.chat("qwen3:4b", [llm.Message.user("Hello")])
print(reply.text)

for let m in try ai.models() {
	print("{m.name} {m.size}")
}
```

## Scope

- Chat and generate with streaming, tools with typed arguments, structured output from a
  struct's schema, images in, thinking on and off, model options (context size, temperature,
  seed, stop), `keepAlive`.
- Embeddings.
- Models: list, show, pull and push with progress, copy, delete, create from a Modelfile,
  running models and their memory.
- Bearer tokens and custom headers for servers behind a proxy.

## Builds on

`std.net.http` (client, newline-delimited JSON streams), `std.data.encoding` (JSON),
`std.ai.llm` (`Message`, `Role`, tool declarations), `std.formats.image` for images in.

## Tests

`adm test ai/ollama` runs against a loopback server. `ADM_TEST_OLLAMA=host:port` adds calls to
a running Ollama; the suite pulls the smallest model it needs.
