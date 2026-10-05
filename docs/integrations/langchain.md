# LangChain

Source: [github.com/hanshs474/langchain-saymaker](https://github.com/hanshs474/langchain-saymaker)

LangChain tools that let an agent generate images and video on [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs): one API key runs Veo 3.1, Kling 3.0, Seedance 2.0, MiniMax H3, Nano Banana 2, GPT Image 2.5, Seedream 5.0 and the rest of the shelf on your own credits.

```bash
pip install langchain-saymaker
export SAYMAKER_API_KEY=sk-...   # create one at https://saymaker.ai/settings/apikeys
```

```python
from langchain_saymaker import SayMakerImageGenerator, SayMakerVideoGenerator

image = SayMakerImageGenerator()
video = SayMakerVideoGenerator()  # MiniMax H3 Fast by default; model="veo-3-1" on a plan

image.invoke({"prompt": "a paper boat at sunrise, soft fog, 35mm film", "aspect_ratio": "16:9"})
video.invoke({"prompt": "a fox trotting through fresh snow at dawn, tracking shot", "duration": 6})
```

Hand both to any tool-calling agent:

```python
from langchain.agents import create_agent

agent = create_agent("openai:gpt-5", tools=[image, video])
agent.invoke({"messages": [("user", "Make a 16:9 cover image of a lighthouse in fog, then a 6-second clip of the beam sweeping")]})
```

## Tools

| Tool | Input | Returns |
|---|---|---|
| `saymaker_image_generator` | `prompt`, optional `aspect_ratio`, optional `image_url` to edit a photo | URL of the image |
| `saymaker_video_generator` | `prompt`, optional `image_url` (first frame), `duration`, `aspect_ratio` | URL of the clip |

For an edit, the prompt names the change and what must stay ("change the jacket to dark green, keep the face and background"). That second half is what decides whether the edit holds.

Refusals come back to the agent as text rather than an exception: out of credits, a model that needs a plan, another run still rendering, or a prompt the filter refused. The agent can reword, wait or switch model instead of crashing the run.

Set `model=` on either tool for a specific engine: `gpt-image-2-5-flare`, `seedream-5-pro`, `nano-banana-2` for images; `veo-3-1`, `kling-3-0`, `seedance-2` for video ([pricing](https://saymaker.ai/pricing?utm_source=readthedocs&utm_medium=docs)). Runs on a free account wait in a queue before they start; the default timeouts cover the longest wait.

Built on the [`saymaker`](https://pypi.org/project/saymaker/) client. MIT licensed. Made by [SayMaker](https://saymaker.ai/?utm_source=readthedocs&utm_medium=docs), the AI video generator agent.
