# adm.ai.gemini

A Gemini API client in ADM over `std.net.http`, for Google's models through an API key. The
same models under Google Cloud credentials are `adm.cloud.gcp.vertex`.

```bash
adm get admlang:adm.ai.gemini
```

```adm
use adm.ai.gemini

let ai = new gemini.Client(apiKey)              // GEMINI_API_KEY when left out
let reply = try ai.chat("gemini-2.5-flash", "What is in this picture?", images = [picture])
print(reply.text)

let vectors = try ai.embed("gemini-embedding-001", ["one", "two"])
```

## Tests

`adm test ai/gemini` runs against a loopback server. `ADM_TEST_GEMINI_KEY` adds calls to the
real API with the smallest model.
