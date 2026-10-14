# adm.ai.ollama

An Ollama client in ADM over `std.net.http`: the native API of a local or remote Ollama
server. `std.ai.llm` loads GGUF files itself; this library is for programs that let Ollama
hold the models and the GPU.

```bash
adm get admlang:adm.ai.ollama
```

```adm
use adm.ai.ollama

let ai = new ollama.Client("http://127.0.0.1:11434")   // OLLAMA_HOST, or this, when left out
try ai.pull("qwen3:4b")
let reply = try ai.chat("qwen3:4b", "Hello")
print(reply.text)

for let m in try ai.models() {
	print("{m.name} {m.size}")
}
```

## Tests

`adm test ai/ollama` runs against a loopback server. `ADM_TEST_OLLAMA=host:port` adds calls to
a running Ollama; the suite pulls the smallest model it needs.
