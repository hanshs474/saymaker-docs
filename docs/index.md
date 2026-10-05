# SayMaker API and SDKs

[SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs) runs image and video models behind one account: Veo 3.1, Kling 3.0, Seedance 2.0 and 2.5, MiniMax H3, Wan 3.0, LTX 2.5, Nano Banana 2, GPT Image 2.5, Seedream 5.0, Qwen Image 3 and more. One API key runs all of them from your own code, on the same credits, prices and plan as the website.

## Start

1. Sign up on [saymaker.ai](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs); new accounts get sign-up credits.
2. Create a key at [saymaker.ai/settings/apikeys](https://saymaker.ai/settings/apikeys?utm_source=readthedocs&utm_medium=docs).
3. `export SAYMAKER_API_KEY=sk-...` and pick a client:

| Language | Install |
|---|---|
| [Python](sdk/python.md) | `pip install saymaker` |
| [JavaScript / TypeScript](sdk/typescript.md) | `npm install saymaker` |
| [Go](sdk/go.md) | `go get github.com/hanshs474/saymaker-go` |
| [Rust](sdk/rust.md) | `cargo add saymaker` |
| [Java / Kotlin](sdk/java.md) | `io.github.hanshs474:saymaker:0.1.0` |
| [.NET](sdk/dotnet.md) | `dotnet add package SayMaker` |
| [PHP](sdk/php.md) | `composer require hanshs474/saymaker` |
| [Ruby](sdk/ruby.md) | `gem install saymaker` |
| [Dart / Flutter](sdk/dart.md) | `dart pub add saymaker` |
| [Elixir](sdk/elixir.md) | `{:saymaker, "~> 0.1"}` |
| [Clojure](sdk/clojure.md) | `io.github.hanshs474/saymaker-clj` |
| [R](sdk/r.md) | `install.packages("saymaker", repos = "https://hanshs474.r-universe.dev")` |
| [Lua](sdk/lua.md) | `luarocks install saymaker` |
| [D](sdk/d.md) | `dub add saymaker` |

From an agent: the [MCP server](integrations/mcp.md), [LangChain tools](integrations/langchain.md), [Haystack components](integrations/haystack.md) and the [Vercel AI SDK provider](integrations/ai-sdk.md). From a shell: `docker run --rm -e SAYMAKER_API_KEY san8888/saymaker "a prompt"`.

Every client does the same two things: submit a run, then poll it until there is a file. The [HTTP API](http.md) page describes both calls if you would rather write your own.
