# Rust

Source: [github.com/hanshs474/saymaker-rs](https://github.com/hanshs474/saymaker-rs)

Generate images and video on [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs) from your own code: one API key runs Veo 3.1, Kling 3.0, Seedance 2.0, MiniMax H3, Nano Banana 2, GPT Image 2.5, Seedream 5.0 and the rest of the shelf on your own credits.

```rust
let url = saymaker::generate("a paper-cut layered mountain range at dusk", &Default::default())?;
let clip = saymaker::generate_video("a paper boat drifting down a rain gutter, low angle", &Default::default())?;
```

Each call waits for the run and returns the URL of the finished file. Runs on a free account wait in a queue before they start, and the wait grows with each run of the day; a paid plan starts at once. The default timeouts cover the longest wait.

## Install

```bash
cargo add saymaker
```

Blocking client on `ureq`; no async runtime needed. Set `SAYMAKER_API_KEY` or pass `api_key`.

## API key

Sign up on [saymaker.ai](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs) (new accounts get sign-up credits), create a key at [saymaker.ai/settings/apikeys](https://saymaker.ai/settings/apikeys?utm_source=readthedocs&utm_medium=docs), then pass it or export it:

```bash
export SAYMAKER_API_KEY=sk-...
```

A key runs on your account exactly like the site does: same models, same credit prices, same plan, and every run lands in your library at [saymaker.ai/history](https://saymaker.ai/history?utm_source=readthedocs&utm_medium=docs).

## Images

```rust
use saymaker::{generate, Options};

let url = generate("isometric diorama of a ramen shop at night", &Options {
    aspect_ratio: Some("16:9".into()),
    model: Some("gpt-image-2-5-flare".into()),
    ..Default::default()
})?;
```

The default model is `saymaker-image-v1`, the cheapest text-to-image run. Pass a model id for `nano-banana-2`, `gpt-image-2-5-flare`, `seedream-5-pro`, `qwen-image-3-pro` and the rest; see the [image models](https://saymaker.ai/image?utm_source=readthedocs&utm_medium=docs).

### Editing a photo

Pass an image URL and the prompt describes the change rather than the picture:

```rust
let url = saymaker::generate(
    "change the jacket to dark green, keep the face, pose and background exactly as they are",
    &saymaker::Options { image_url: Some("https://example.com/portrait.jpg".into()), ..Default::default() },
)?;
```

Naming what must stay is what decides whether the edit holds. Prompts that only name the change tend to drift the whole picture.

## Video

```rust
use saymaker::{generate_video, VideoOptions};

let clip = generate_video("a fox trotting through fresh snow at dawn, tracking shot",
    &VideoOptions { duration: Some(6), ..Default::default() })?;
let clip = generate_video("the camera slowly orbits the statue",
    &VideoOptions { image_url: Some("https://example.com/statue.jpg".into()), ..Default::default() })?;
let clip = generate_video("a street drummer in the rain",
    &VideoOptions { model: Some("veo-3-1".into()), resolution: Some("1080p".into()), ..Default::default() })?;
```

The default model is `minimax-h3-fast`, the one a free account can run (480p or 768p, 4 to 15 seconds). Veo 3.1, Kling 3.0, Seedance 2.0 and the other video models need a plan or a credit pack; see [pricing](https://saymaker.ai/pricing?utm_source=readthedocs&utm_medium=docs). Each model's options are on its page: [Veo 3.1](https://saymaker.ai/video/veo-3-1?utm_source=readthedocs&utm_medium=docs), [Kling 3.0](https://saymaker.ai/video/kling-3-0?utm_source=readthedocs&utm_medium=docs), [Seedance 2.0](https://saymaker.ai/video/seedance-2?utm_source=readthedocs&utm_medium=docs).

## Errors worth catching

- `Error::Auth` — no key, or the key is wrong or deleted
- `Error::Quota` — the account is out of credits
- `Error::Plan` — that model needs a paid plan or a credit pack
- `Error::Busy` — another run on this account is still rendering; free accounts run one at a time
- `Error::Rejected` — the content filter or the model refused the prompt; rewrite it rather than retrying
- `Error::Timeout` — the deadline passed; the run may still finish in your history

The API answers refusals with HTTP 200 and an error code in the body, so a client that only checks the status code reports a refusal as success. This one reads the body.

Free-account output carries a watermark; a paid plan returns the clean file.

MIT licensed. Made by [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs), the AI video generator agent.
