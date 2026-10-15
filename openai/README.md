# adm.ai.openai

An OpenAI API client in ADM over `std.net.http`. It also talks to the servers that speak the
same API (OpenRouter, Groq, Mistral, DeepSeek, Together, vLLM, LM Studio, Ollama) through
`baseURL`. Its conversations are `std.ai.llm`'s, so tools, `@tool` methods, MCP tools,
pictures and documents work as they do with a model loaded by `std.ai.llm` itself.

```bash
adm get admlang:adm.ai.openai
```

```adm
use adm.ai.openai

let ai = new openai.Client(model = "gpt-5-mini")    // OPENAI_API_KEY; baseURL for another server
let chat = ai.chat()
chat.system = "Answer in one sentence."
chat.useTool(new Weather())
print(try chat.say("Do I need a coat in Oslo today?"))

chat.model = "gpt-5"                                 // the conversation goes on with another model
try chat.ask("Why?")
for {
	let piece = try chat.next()
	break when piece is none
	print(piece)
}

let vectors = try ai.embedder("text-embedding-3-small").embed(string["one", "two"])
let pictures = try ai.images.generate("a kettle on a windowsill", size = openai.ImageSize.Square)
let words = try ai.audio.transcribe(recording)
let file = try ai.files.upload("requests.jsonl", openai.Purpose.Batch)
let batch = try ai.batches.create(file.id)
```

On OpenAI `chat()` speaks the Responses API, carrying each reply's reasoning to the next
request; on other servers it speaks Chat Completions (`api` chooses). A conversation is a
`llm.Chat` and an embedder a `std.ai.embeddings.Embedder`. The client also covers
`images.edit`/`vary`, `audio.translate`/`speak`, `files`, `vectorStores` (with search),
`batches` and `models`; `effort`, `verbosity`, `serviceTier`, `summarize`,
`parallelToolCalls`, `organization` and `project` are settings of the client.

## Tests

`adm test ai/openai` runs against a loopback server. `ADM_TEST_OPENAI_KEY` adds calls to OpenAI
with gpt-5-mini and text-embedding-3-small; `ADM_TEST_OPENAI_URL` points them at a compatible
server instead (`http://127.0.0.1:11434/v1` runs them on a local Ollama with qwen3:0.6b and
all-minilm). `ADM_TEST_OPENAI_MODEL` and `ADM_TEST_OPENAI_EMBED` choose other models.
