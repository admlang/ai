# adm.ai.openai

An OpenAI API client in ADM over `std.net.http`. It also talks to every server that speaks the
same API: OpenRouter, Groq, Mistral, DeepSeek, Together, vLLM, LM Studio and Ollama's
compatible endpoint, through `baseURL`.

```bash
adm get admlang:adm.ai.openai
```

```adm
use adm.ai.openai

let ai = new openai.Client(apiKey)              // Secret<string>; OPENAI_API_KEY when left out
let reply = try ai.chat("gpt-5-mini", "Name three prime numbers")   // or a list of llm.Message
print(reply.text)

let stream = try ai.chatStream("gpt-5-mini", messages)
for let piece in stream {
	print(piece.text)
}

let vectors = try ai.embed("text-embedding-3-small", ["one", "two"])   // float[][]
```

## Tests

`adm test ai/openai` runs against a loopback server. `ADM_TEST_OPENAI_KEY` adds calls to the
real API with the smallest models; `ADM_TEST_OPENAI_URL` points the same tests at a compatible
server.
