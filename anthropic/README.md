# adm.ai.anthropic

An Anthropic API client in ADM over `std.net.http`: the Messages API of the Claude models. Its
conversations are `std.ai.llm`'s, so tools, `@tool` methods, MCP tools, pictures and documents
work as they do with a model loaded by `std.ai.llm` itself.

```bash
adm get admlang:adm.ai.anthropic
```

```adm
use adm.ai.anthropic

let ai = new anthropic.Client(model = "claude-opus-5-5")    // ANTHROPIC_API_KEY
let chat = ai.chat()
chat.system = "Answer in one sentence."
chat.reasoning = llm.Reasoning.High
chat.useTool(new Weather())
print(try chat.say("Do I need a coat in Oslo today?"))
print(chat.messages()[1].thinking)

chat.model = "claude-haiku-5-5"                             // the conversation goes on with another model
try chat.ask("Why?")
for {
	let piece = try chat.next()
	break when piece is none
	print(piece)
}
print(chat.usage().cachedTokens)

print(try ai.countTokens(chat))
let file = try ai.files.upload("report.pdf", "application/pdf")
let batch = try ai.batches.create([anthropic.BatchRequest{id: "q1", request: request}])

ai.webSearch = anthropic.WebSearch{maxUses: 3}                // tools Anthropic runs
ai.codeExecution = true
ai.memory = "notes"                                         // the memory tool over a folder
ai.clearToolUses = anthropic.ClearToolUses{atTokens: 100000}  // context editing
let research = ai.chat()
print(try research.say("What changed in the EU AI Act this year?"))
for let (_, cite) in anthropic.citationsOf(research.messages()[1]) {
	print("{cite.title}: {cite.url}")
}
try ai.compact(research)                                    // the conversation summed up
```

A conversation is a `llm.Chat`. Each reply's content goes back to the API as it came (signed
thinking, calls), the conversation is cached between requests (`caching`), and a reply the
safety classifiers decline on the models that allow it is served by another model in the same
call (`refusalFallback`). The client also covers `models`, `files` and `batches` (with
`results`); `effort`, `display`, `thinkingBudget` (for models before Claude 4.6),
`eagerToolInput`, `parallelToolCalls`, `userId`, `serviceTier` and `betas` are settings of the
client.

Server tools are settings too: `webSearch`, `webFetch` (with `filtering` for dynamic
filtering on Claude 4.6 and later) and `codeExecution`, whose container the next request goes
on in. `serverToolsOf(reply)` lists what they did, `citationsOf(reply)` what the reply cites
(web pages, document characters, pages, blocks), `outputFilesOf(reply)` the files code wrote;
commands and their output stream as text, and a turn the API pauses goes on in the same reply.
`memory` offers Anthropic's memory tool over a folder (`memoryTool`); `computer` the computer
use toolset over the program's own `Computer` (screenshots and actions). `clearToolUses` and
`clearThinking` turn on context editing (`editsOf(reply)`), `compactAt` compaction at a size,
and `compact(chat)` sums a conversation up on demand.

## Tests

`adm test ai/anthropic` runs against a loopback server. `ADM_TEST_ANTHROPIC_KEY` adds calls to
the real API with `ADM_TEST_ANTHROPIC_MODEL` (default claude-haiku-5-5).
