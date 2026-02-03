# Gemini-OpenAI 2
[English](https://github.com/Komorebi-yaodong/openai-gemini) | [简体中文](https://github.com/Komorebi-yaodong/openai-gemini/blob/main/README_zh.md) | [原项目仓库](https://github.com/PublicAffairs/openai-gemini)

## 为什么需要这个项目？

Gemini API 是[免费的](https://ai.google.dev/pricing "有使用限制！")，但许多现有工具链都只支持 OpenAI API。

这个项目为您提供了一个个人使用、免费且与 OpenAI API 兼容的 Gemini 代理端点。


## 无服务器（Serverless）？

是的。尽管它运行在云端，但完全无需您进行服务器维护。您可以轻松地将其免费部署到各种云服务提供商（它们为个人使用提供了非常慷慨的免费额度）。

> [!TIP]
> 您也可以在本地运行此代理端点，这更适合开发和调试。


## 如何开始

首先，您需要一个 Google [API 密钥](https://makersuite.google.com/app/apikey)。

> [!IMPORTANT]
> 即使您所在的地区[不受支持](https://ai.google.dev/gemini-api/docs/available-regions#available_regions)，
> 仍然可以通过 VPN 获取 API 密钥。

接下来，请根据以下说明，将此项目部署到您选择的一个云服务提供商。您需要先注册一个该平台的账户。

如果您选择“一键部署”，平台会引导您先 Fork 本仓库，这是实现持续集成（CI）所必需的步骤。


### 部署到 Vercel

 [![使用 Vercel 部署](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https://github.com/Komorebi-yaodong/openai-gemini&repository-name=my-openai-gemini)
- 或者，使用 [Vercel CLI](https://vercel.com/docs/cli) 部署： `vercel deploy`
- 在本地运行： `vercel dev`
- Vercel _Functions_ 的[限制](https://vercel.com/docs/functions/limitations) (使用 _Edge_ 运行时)


### 部署到 Netlify

[![部署到 Netlify](https://www.netlify.com/img/deploy/button.svg)](https://app.netlify.com/start/deploy?repository=https://github.com/Komorebi-yaodong/openai-gemini&integrationName=integrationName&integrationSlug=integrationSlug&integrationDescription=integrationDescription)
- 或者，使用 [Netlify CLI](https://docs.netlify.com/cli/get-started/) 部署： `netlify deploy`
- 在本地运行： `netlify dev`
- Netlify 提供了两种 API 地址：
  - `/v1` (例如 `/v1/chat/completions` 端点)  
    _Functions_ [限制](https://docs.netlify.com/functions/get-started/?fn-language=js#synchronous-function-2)
  - `/edge/v1`  
    _Edge functions_ [限制](https://docs.netlify.com/edge-functions/limits/)


### 部署到 Cloudflare

[![部署到 Cloudflare Workers](https://deploy.workers.cloudflare.com/button)](https://deploy.workers.cloudflare.com/?url=https://github.com/Komorebi-yaodong/openai-gemini)
- 或者，手动将源代码文件的内容粘贴到 https://workers.cloudflare.com/playground 并点击 `部署`。
- 或者，使用 [Wrangler CLI](https://developers.cloudflare.com/workers/wrangler/) 部署： `wrangler deploy`
- 在本地运行： `wrangler dev`
- _Worker_ 的[限制](https://developers.cloudflare.com/workers/platform/limits/#worker-limits)


### 部署到 Deno

详情请见 [这里](https://github.com/PublicAffairs/openai-gemini/discussions/19)。


### 在本地运行 - 使用 Node, Deno, Bun

仅 Node 需要：`npm install`。

然后执行 `npm run start` / `npm run start:deno` / `npm run start:bun`。


#### 开发模式 (监听文件变动)

仅 Node 需要：`npm install --include=dev`

然后执行: `npm run dev` / `npm run dev:deno` / `npm run dev:bun`。


## 如何使用
当您在浏览器中打开新部署的网站时，只会看到 `404 Not Found` 消息。这是正常现象，因为此 API 并非为浏览器直接访问而设计。
要使用它，您需要将您的 API 地址和 Gemini API 密钥填入您所使用的软件的相关设置中。

> [!NOTE]
> 并非所有软件都支持覆盖 OpenAI 端点，但许多工具都提供了这个功能（尽管有时这些设置可能隐藏得比较深）。

通常，您需要按以下格式指定 API Base 地址：
`https://my-super-proxy.vercel.app/v1`

相关设置项可能被标记为“_OpenAI 代理_”或“_OpenAI Proxy_”。
您可能需要在“_高级设置_”或类似区域寻找。
或者，它可能位于某个配置文件中（请查阅相关工具的文档）。

对于某些命令行工具，您可能需要设置环境变量，例如：
```sh
OPENAI_BASE_URL="https://my-super-proxy.vercel.app/v1"
```
或者：
```sh
OPENAI_API_BASE="https://my-super-proxy.vercel.app/v1"
```


## 模型

如果请求中指定的 [model] 名称以 "gemini-"、"gemma-"、"learnlm-" 或 "models/" 开头，则会使用该模型。否则，将应用以下默认模型：

- `chat/completions`: `gemini-2.5-flash`
- `embeddings`: `gemini-embedding-001`
- `audio/speech`: `gemini-2.5-flash-preview-tts`

[model]: https://ai.google.dev/gemini-api/docs/models#model-variations

## 推理（思考）

本代理支持 `reasoning_effort` 参数，用于控制 Gemini 模型的内部“思考”过程，它会被映射到 Gemini 的 `thinkingBudget`。

- `reasoning_effort: "low"`: 使用最低的思考预算。对于 "pro" 模型，此值为 `128`；对于其他模型（如 Flash），此值为 `0`（禁用思考）。适用于需要快速响应的简单任务。
- `reasoning_effort: "medium"` (或未指定): 启用动态思考 (`-1`)，允许模型根据请求的复杂性自动调整思考预算。这是 Gemini 2.5 模型的默认行为。
- `reasoning_effort: "high"`: 设置一个高思考预算 (`24576`)，用于需要深度推理和规划的最复杂任务。

## 内置工具

### 网页搜索
要使用**网页搜索**工具，请在模型名称后附加 `:search`
(例如, `gemini-2.5-flash:search`)。

### URL 上下文
要提供一个 URL 作为上下文，请在模型名称后附加 `:url` (例如, `gemini-2.5-flash:url`)。URL 应包含在用户的消息中。

### 代码执行
要启用**代码执行**工具，请在模型名称后附加 `:execode` (例如, `gemini-2.5-flash:execode`)。

### 图像生成
要使用**图像生成**工具，请指定一个包含 `-image` 的模型名称。响应将在 `chat/completions` 的消息内容中以 Markdown 图像字符串的形式返回，格式如下： `![gemini-image-generation](data:image/png;base64,...)`。

## 文本转语音 (TTS)

本代理支持两种方式生成语音，均映射到 Gemini 的音频生成能力。

### 方式一：标准 `/v1/audio/speech` 端点

这是标准的 OpenAI 兼容方法。

- **端点**: `/v1/audio/speech`
- **方法**: `POST`
- **支持的参数**: `input`, `voice`, `model`, `response_format`。
- **支持的 `response_format`**:
    - 原生支持 `wav` (带头部) 和 `pcm` (原始音频数据)。
    - 如果请求 `mp3`, `opus`, `aac` 或 `flac` 等格式，将自动**回退到 `wav` 格式**。此时，响应头中会包含一个 `X-Warning` 来提示此回退。

### 方式二：通过 `/v1/chat/completions` 端点

本代理还支持一种非标准方式，即在聊天补全请求中包含 `modalities` 字段来触发 TTS。音频数据将返回在标准的聊天补全响应结构中。

- **端点**: `/v1/chat/completions`
- **触发方式**: 在请求体中包含 `"modalities": ["audio"]`。
- **输入**: `messages` 数组中最后一条消息的文本内容将被用作输入。
- **声音**: 在请求体中使用 `audio.voice` 指定声音 (例如, `{"audio": {"voice": "Zephyr"}}`)。
- **响应**: 响应将是一个标准的 `chat.completion` 对象，其中助手的消息会包含一个带有 base64 编码数据的 `audio` 对象。

## 多媒体（视觉和音频输入）

本代理支持 OpenAI [规范]中的[视觉]和[音频]输入。
此功能通过 Gemini 的 [`inlineData`](https://ai.google.dev/api/caching#Part) 实现。

[视觉]: https://platform.openai.com/docs/guides/images-vision?api-mode=chat&format=url#giving-a-model-images-as-input
[音频]: https://platform.openai.com/docs/guides/audio?example=audio-in&lang=curl#add-audio-to-your-existing-application
[规范]: https://platform.openai.com/docs/api-reference/chat/create


## Gemini 特定功能

Gemini 支持一些 OpenAI 模型没有的特性，您可以通过 `extra_body.google` 字段来启用它们。

- **`safety_settings`**: 覆盖默认的安全设置。
- **`cached_content`**: 使用缓存的上下文来加速响应。
- **`thinking_config`**: 控制模型的内部推理过程。

关于如何使用这些功能的更多细节，请参阅 [Gemini API 文档](https://ai.google.dev/gemini-api/docs/openai#extra-body)。

---

## 支持的 API 端点和参数

- [x] `chat/completions`

  目前，两个 API 都适用的大部分参数都已实现。
  <details>

  - [x] `messages`
      - [x] `content`
      - [x] `role`
          - [x] "system" (=>`system_instruction`)
          - [x] "user"
          - [x] "assistant"
          - [x] "tool"
      - [ ] `name`
      - [x] `tool_calls`
  - [x] `model`
  - [x] `frequency_penalty`
  - [ ] `logit_bias`
  - [ ] `logprobs`
  - [ ] `top_logprobs`
  - [x] `max_tokens`, `max_completion_tokens`
  - [x] `n` (`candidateCount` <8, 不支持流式传输)
  - [x] `presence_penalty`
  - [x] `reasoning_effort`
  - [x] `response_format`
      - [x] "json_object"
      - [x] "json_schema" (OpenAPI 3.0 schema 对象的一个子集)
      - [x] "text"
  - [x] `seed`
  - [x] `stop`: string|array (`stopSequences` [1,5])
  - [x] `stream`
  - [x] `stream_options`
      - [x] `include_usage`
  - [x] `temperature` (OpenAI 为 0.0..2.0, 但 Gemini 支持更广的范围)
  - [x] `top_p`
  - [x] `tools`
  - [x] `tool_choice`
  - [ ] `parallel_tool_calls` (在 Gemini 中默认始终激活)
  - [x] [`extra_body`](#gemini-specific-functions)

  </details>
- [ ] `completions`
- [x] `embeddings`
  - [x] `dimensions`
- [x] `models`
- [x] `audio/speech`