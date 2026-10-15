# adm.ai.gemini

A Gemini API client in ADM over `std.net.http`, for Google's models through an API key. The
same models under Google Cloud credentials are `adm.cloud.gcp.vertex`. Its conversations are
`std.ai.llm`'s, so tools, `@tool` methods, MCP tools, pictures, sound, documents and uploaded
files work as they do with a model loaded by `std.ai.llm` itself.

```bash
adm get admlang:adm.ai.gemini
```

```adm
use adm.ai.gemini

let ai = new gemini.Client(model = "gemini-flash-latest")   // GEMINI_API_KEY
ai.googleSearch = true
let chat = ai.chat()
let answer = try chat.say("What is the weather in Oslo today?")
for let source in gemini.groundingOf(chat.messages()[2]).sources {
	print("{source.title} {source.uri}")
}

let clip = try ai.files.wait((try ai.files.upload("talk.mp4", "video/mp4")).name)
try chat.ask(llm.Message{text: "Summarise the talk.", files: [gemini.remote(clip)]})

let vectors = try ai.embedder(task = gemini.TaskType.RetrievalDocument).embed(string["one", "two"])
```

A conversation is a `llm.Chat` and an embedder a `std.ai.embeddings.Embedder`. Each reply's
parts go back to the API as they came (thought signatures with them). The client also covers
`countTokens`, `models`, `files` (upload, `wait` while processing, list, delete) and `caches`
(context caches: create, extend, delete; `cachedContent` makes a conversation follow one);
`thinkingBudget`, `includeThoughts`, `googleSearch`, `urlContext`, `codeExecution` and `safety`
are settings of the client.

Videos go in a message (`Message.videos`, sent inline), a tool's pictures with its result
(Gemini 3). `batches` runs requests at half the price within a day (create, get, list, cancel,
delete, `results` as messages), and `live()` opens a spoken conversation over the Live API
(`llm.LiveChat`: sound at 16 kHz in, 24 kHz out, transcripts, tools).

## Tests

`adm test ai/gemini` runs against a loopback server. `ADM_TEST_GEMINI_KEY` adds calls to the
real API with `ADM_TEST_GEMINI_MODEL` (default gemini-flash-lite-latest).
