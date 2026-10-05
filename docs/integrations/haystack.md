# Haystack

Source: [github.com/hanshs474/saymaker-haystack](https://github.com/hanshs474/saymaker-haystack)

[Haystack](https://haystack.deepset.ai) components that generate images and video on [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs): one API key runs Veo 3.1, Kling 3.0, Seedance 2.0, MiniMax H3, Nano Banana 2, GPT Image 2.5, Seedream 5.0 and the rest of the shelf on your own credits.

```bash
pip install saymaker-haystack
export SAYMAKER_API_KEY=sk-...   # create one at https://saymaker.ai/settings/apikeys
```

```python
from saymaker_haystack import SayMakerImageGenerator, SayMakerVideoGenerator

SayMakerImageGenerator(aspect_ratio="16:9").run(prompt="a lighthouse in fog, long exposure")
# {"images": ["https://cdn.saymaker.ai/..."]}

SayMakerVideoGenerator(duration=6).run(prompt="the lighthouse beam sweeps across the fog")
# {"videos": ["https://cdn.saymaker.ai/..."]}
```

## In a pipeline

Write the prompt with an LLM, render it with SayMaker:

```python
from haystack import Pipeline
from haystack.components.builders import PromptBuilder
from haystack.components.generators import OpenAIGenerator
from saymaker_haystack import SayMakerImageGenerator

p = Pipeline()
p.add_component("brief", PromptBuilder("Write one image prompt for a blog cover about {{topic}}. Name light, material, framing."))
p.add_component("llm", OpenAIGenerator())
p.add_component("render", SayMakerImageGenerator(aspect_ratio="16:9"))
p.connect("brief", "llm")
p.connect("llm.replies", "render.prompt")
```

(`llm.replies` is a list; add a small adapter component that takes `replies[0]` if your Haystack version does not convert it.)

## Components

| Component | `run()` input | Output |
|---|---|---|
| `SayMakerImageGenerator` | `prompt`, optional `image_url` to edit a photo | `{"images": [url]}` |
| `SayMakerVideoGenerator` | `prompt`, optional `image_url` (first frame) | `{"videos": [url]}` |

Both serialize with `to_dict` / `from_dict`; the key is a Haystack `Secret` read from `SAYMAKER_API_KEY`.

`SayMakerVideoGenerator` defaults to `minimax-h3-fast`, the one a free account can run. Pass `model="veo-3-1"`, `"kling-3-0"` or `"seedance-2"` on a plan or credit pack ([pricing](https://saymaker.ai/pricing?utm_source=readthedocs&utm_medium=docs)). Runs on a free account wait in a queue before they start; the default timeouts cover the longest wait.

Built on the [`saymaker`](https://pypi.org/project/saymaker/) client. MIT licensed. Made by [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs), the AI video generator agent.
