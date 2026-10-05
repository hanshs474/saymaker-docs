# Vercel AI SDK

Source: [github.com/hanshs474/saymaker-ai-provider](https://github.com/hanshs474/saymaker-ai-provider)

[SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs) provider for the [Vercel AI SDK](https://ai-sdk.dev): `generateImage` and `generateVideo` on Veo 3.1, Kling 3.0, Seedance 2.0, MiniMax H3, Nano Banana 2, GPT Image 2.5, Seedream 5.0 and the rest of the SayMaker shelf, on one API key and your own credits.

```bash
npm install saymaker-ai-provider ai
export SAYMAKER_API_KEY=sk-...   # create one at https://saymaker.ai/settings/apikeys
```

## Images

```ts
import { generateImage } from 'ai';
import { saymaker } from 'saymaker-ai-provider';

const { image } = await generateImage({
  model: saymaker.image('gpt-image-2-5-flare'),
  prompt: 'isometric diorama of a ramen shop at night',
  aspectRatio: '16:9',
});
```

`saymaker.image()` with no id is `saymaker-image-v1`, the cheapest text-to-image run. Other ids: `nano-banana-2`, `nano-banana-pro`, `seedream-5-pro`, `qwen-image-3-pro`, `grok-imagine-image-2-0`; see the [image models](https://saymaker.ai/image?utm_source=readthedocs&utm_medium=docs).

To edit a photo, pass its URL and describe the change and what must stay:

```ts
await generateImage({
  model: saymaker.image('nano-banana-2'),
  prompt: 'change the jacket to dark green, keep the face, pose and background exactly as they are',
  providerOptions: { saymaker: { imageUrl: 'https://example.com/portrait.jpg' } },
});
```

## Video

```ts
import { experimental_generateVideo as generateVideo } from 'ai';

const { video } = await generateVideo({
  model: saymaker.video('veo-3-1'),
  prompt: 'a street drummer in the rain, slow push-in',
  duration: 8,
  resolution: '1920x1080',
});
```

Pass `image: { type: 'url', url }` to animate a still as the first frame. `saymaker.video()` with no id is `minimax-h3-fast`, the model a free account can run (480p or 768p, 4 to 15 seconds). Veo 3.1, Kling 3.0, Seedance 2.0 and the others need a plan or a credit pack ([pricing](https://saymaker.ai/pricing?utm_source=readthedocs&utm_medium=docs)); each model's durations and resolutions are on its page, e.g. [Veo 3.1](https://saymaker.ai/video/veo-3-1?utm_source=readthedocs&utm_medium=docs), [Kling 3.0](https://saymaker.ai/video/kling-3-0?utm_source=readthedocs&utm_medium=docs), [Seedance 2.0](https://saymaker.ai/video/seedance-2?utm_source=readthedocs&utm_medium=docs).

## Settings

```ts
import { createSayMaker } from 'saymaker-ai-provider';

const sm = createSayMaker({ apiKey: process.env.MY_KEY, timeoutMs: 20 * 60_000 });
```

Runs on a free account wait in a queue before they start, and the wait grows with each run of the day; a paid plan starts at once. The default timeouts cover the longest wait.

Failures throw `SayMakerError` with a `reason`: `auth` (key missing or wrong), `quota` (out of credits), `plan` (model needs a plan or pack), `busy` (another run on this account is still rendering), `rejected` (reword the prompt). The API answers refusals with HTTP 200 and an error code in the body; this provider reads the body.

MIT licensed. Made by [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs), the AI video generator agent.
