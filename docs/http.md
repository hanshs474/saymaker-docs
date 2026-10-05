# HTTP API

Two endpoints. Both take JSON, both need `Authorization: Bearer <key>` ([create a key](https://saymaker.ai/settings/apikeys?utm_source=readthedocs&utm_medium=docs)).

## Submit a run

```bash
curl https://saymaker.ai/api/ai/generate \
  -H "Authorization: Bearer $SAYMAKER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"provider":"kie","mediaType":"image","model":"saymaker-image-v1","scene":"text-to-image","prompt":"a lighthouse in fog, cinematic","options":{"size":"16:9","resolution":"1K","n":1}}'
```

| Field | Values |
|---|---|
| `mediaType` | `image` or `video` |
| `scene` | `text-to-image`, `image-to-image`, `text-to-video`, `image-to-video` |
| `model` | a model id from the [models](models.md) table |
| `provider` | the routing field listed next to the model id in the [models](models.md) table |
| `options` | images: `size` (aspect ratio, or `auto` for an edit), `resolution`, `n`, `image_input` (list of URLs). Video: `aspect_ratio`, `duration`, `resolution`, `sound`, `image_input` |

The answer is `{"code":0,"data":{"id":"...","costCredits":5,"status":"pending"}}`. Keep `data.id`.

## Poll it

```bash
curl https://saymaker.ai/api/ai/query \
  -H "Authorization: Bearer $SAYMAKER_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"taskId":"<data.id>"}'
```

Poll every 5 seconds for images, 10 for video, until `data.images`, `data.cleanImages` (the unwatermarked file on a paid plan) or `data.videos` has a URL, or `data.status` is `failed`.

Runs on a free account wait in a queue before they start, and the wait grows with each run of the day; a paid plan starts at once. The queued run only reaches the model when a poll crosses the end of the wait, so keep polling: a client that stops polling never gets its run started.

## Refusals look like successes

Every answer is HTTP 200. Read `code`: anything other than `0` is a refusal, and `message` says why:

| `message` contains | Meaning |
|---|---|
| `invalid API key` | the key is wrong or deleted |
| `insufficient credits` | the account is out of credits |
| `subscription`, `plan` | that model needs a paid plan or a credit pack |
| `is rendering`, `at once` | another run on this account is still going; free accounts run one at a time |

A request with no key at all comes back as `code: 0` with `data.wall: true` and no `id`.
