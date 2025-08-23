# 一、端点与基本请求体

- **OpenAI（Chat Completions）**
  `POST /v1/chat/completions`
  典型请求：

  ```json
  {
    "model": "gpt-4.1",
    "messages": [{"role": "user", "content": "Hello!"}],
    "max_tokens": 1024,          // 可选但常用
    "temperature": 0.2,          // 可选
    "tools": [ ... ]             // 可选：函数/工具
  }
  ```

  返回主体字段集中在 `choices[0].message`，外层包含 `id/model/created/usage` 等元数据。([OpenAI 平台][1])

- **Anthropic（Messages API）**
  `POST /v1/messages`
  典型请求：

  ```json
  {
    "model": "claude-3-5-sonnet-20240620",
    "max_tokens": 1024, // 必填
    "messages": [{ "role": "user", "content": "Hello, world" }],
    "system": "可选系统提示词（顶层）"
  }
  ```

  返回顶层直接给出 `content`（内容块数组）、`stop_reason`、`id`、`model` 等；不走 `choices[]`。([Anthropic][2])

> **核心结构差异**
>
> - OpenAI：`choices[] -> message -> content`（数组用于多选项/采样）；
> - Anthropic：顶层 `content: [{type: "text"|...}]`，并用 `stop_reason` 表示停止原因。([Anthropic][2])

# 二、角色与系统提示词

- **OpenAI**：消息 `role` 支持 `system/user/assistant/tool`，系统提示通常作为第一条 `system` 消息放入 `messages`。([OpenAI 平台][1])
- **Anthropic**：会话只接受 `user/assistant` 轮次；**系统提示通过顶层 `system` 字段**传入（也支持内容块形式）。([Anthropic][2])

# 三、内容块与多模态（图像等）

- **OpenAI**：多模态在 `messages[].content` 内采用**数组**，元素可为 `{type:"text", text:"..."}` 与 `{type:"image_url", image_url:{url:"..."}}`；也支持 base64（同结构、`image_url.url` 写成 Data URL）。([OpenAI 平台][3], [Microsoft Learn][4])
- **Anthropic**：多模态使用 `content` **块类型**：`{type:"image", source:{type:"base64"|"url", media_type:"image/jpeg", data|url:...}}`；同时也支持 Files API/attachments 复用上传的文件。([Anthropic][5])

# 四、函数/工具调用（Tool Calling）

- **OpenAI**（工具=函数）：

  - 在请求里提供 `tools: [{type:"function", function:{name, description, parameters(JSON Schema)}}]`；
  - 模型返回 `tool_calls`（可能**并行多个**），每个含 `id`、`function.name/arguments(JSON字符串)`；
  - 你执行函数后，将结果作为新的 `message`（`role:"tool"`, `tool_call_id`, `content`）再发回模型继续。([OpenAI 平台][6])

- **Anthropic**（工具=自定义 API）：

  - 顶层提供 `tools:[{name, description, input_schema}]`；
  - 如果需要用工具，Claude 会在响应 `content` 中插入 **`{type:"tool_use", name, id, input}` 内容块**，并将 `stop_reason` 设为 `tool_use`；
  - 你执行后，把结果放进下一次请求的 `messages` 里，作为 `user` 角色的 **`{type:"tool_result", tool_use_id: <对应id>, content:"..."}`** 内容块返回。([Anthropic][7], [AWS 文档][8])

> **对齐要点**
>
> - OpenAI 的工具调用粘在 `assistant` 消息的 `tool_calls` 字段；
> - Anthropic 把工具请求与结果都**内嵌在内容块**里（`tool_use`/`tool_result`），类型化更强。([Anthropic][7])

# 五、流式（SSE）差异

- **OpenAI**：`stream: true` 时返回 `chat.completion.chunk` 事件；增量在 `choices[].delta`，工具调用会分片增量写入 `delta.tool_calls[].function.arguments`。收尾含一个最终的 `finish_reason`。([OpenAI 平台][9])
- **Anthropic**：事件粒度更细，有 `message_start`、`content_block_start`、`content_block_delta`、`message_delta`、`message_stop` 等，便于区分文本块与 `tool_use` 块的开始/增量/结束。

# 六、停止原因与采样控制

- **OpenAI**：`finish_reason` 常见值 `stop|length|content_filter|tool_calls` 等；支持 `stop`, `temperature`, `top_p` 等采样参数。([OpenAI 平台][1])
- **Anthropic**：`stop_reason`（如 `end_turn|max_tokens|tool_use` 等）；采样参数类似（`temperature`, `top_p`, `top_k`），并另有 `stop_sequences`。([Anthropic][2])

# 七、用量统计与元数据

- **OpenAI**：非流式响应带 `usage: {prompt_tokens, completion_tokens, total_tokens}`；部分 SDK/场景在流式完结后提供最终用量。([OpenAI 平台][1])
- **Anthropic**：响应含 `usage: {input_tokens, output_tokens}`；流式需在 `message_stop` 后整合。([Anthropic][2])

# 八、错误与约束

- **OpenAI**：标准 HTTP 状态码 + 错误对象；`max_tokens` 可省略，但建议显式控制；多模型族（如 GPT-4o/4.1/等）在能力与上下文窗口上不同。([OpenAI 平台][1])
- **Anthropic**：`max_tokens` **必填**；错误同样按标准 JSON 返回；不同 Claude 代际（3 / 3.5 Sonnet 等）上下文窗、图像/工具能力不同，具体见模型页。([Anthropic][2])

# 九、最小迁移映射表

| 能力         | OpenAI(Chat)                                                  | Anthropic(Messages)                                            |                         |          |
| ------------ | ------------------------------------------------------------- | -------------------------------------------------------------- | ----------------------- | -------- |
| 端点         | `/v1/chat/completions`                                        | `/v1/messages`                                                 |                         |          |
| 系统提示     | `messages[0].role="system"`                                   | 顶层 `system`                                                  |                         |          |
| 多模态       | `content: [{type:"text"},{type:"image_url",image_url:{url}}]` | \`content: \[{type:"text"},{type:"image",source:{type:"base64" | "url", media_type, data | url}}]\` |
| 工具定义     | `tools: [{type:"function", function:{name,parameters}}]`      | `tools: [{name, input_schema, ...}]`                           |                         |          |
| 工具请求     | `assistant.tool_calls[]`                                      | `content[]` 中的 `{type:"tool_use"}`                           |                         |          |
| 工具结果回传 | 新增 `role:"tool"` 消息（含 `tool_call_id`）                  | 下一轮 `user` 消息里放 `{type:"tool_result", tool_use_id}`     |                         |          |
| 停止原因     | `finish_reason`                                               | `stop_reason`                                                  |                         |          |
| 流式增量     | `choices[].delta`                                             | `content_block_delta` 等多事件                                 |                         |          |

（详见文档原文以对应字段名与事件名）([OpenAI 平台][1], [Anthropic][2])

# 十、双提供商适配器（TypeScript）示例

> 思路：在业务层定义统一的**会话与工具抽象**，将 OpenAI 与 Anthropic 的差异封装在 provider 适配器中。下面代码片段演示**发送/接收 + 工具调用回合**的最小实现骨架（省略鉴权与重试）。

```ts
// 统一消息与工具类型
type Role = "system" | "user" | "assistant" | "tool";
type Msg = { role: Exclude<Role, "system">; content: string };
type ToolSpec = { name: string; description?: string; parameters: any };
type ToolCall = { id: string; name: string; arguments: any };

// Provider 接口
interface LLMProvider {
  create(
    messages: Msg[],
    opts: {
      system?: string;
      maxTokens: number;
      tools?: ToolSpec[];
      stream?: boolean;
    }
  ): Promise<{
    text?: string;
    toolCalls?: ToolCall[]; // 可能有并行多个
    stopReason?: string;
    usage?: { inputTokens: number; outputTokens: number };
    // 如果是流式，也可暴露一个 onDelta 回调
  }>;
  // 把工具结果回填给模型继续
  continueWithToolResults(args: {
    prevMessages: Msg[];
    toolResults: { id: string; result: string }[];
    system?: string;
    maxTokens: number;
    tools?: ToolSpec[];
  }): Promise<{ text?: string; toolCalls?: ToolCall[] }>;
}
```

**OpenAI 适配（核心片段）**

```ts
// -> 请求：messages 转 OpenAI 格式；tools 转 {type:"function", function:{...}}
const payload = {
  model: "gpt-4.1",
  messages: [
    ...(system ? [{ role: "system", content: system }] : []),
    ...messages,
  ],
  max_tokens: maxTokens,
  tools: tools?.map((t) => ({
    type: "function",
    function: {
      name: t.name,
      parameters: t.parameters,
      description: t.description,
    },
  })),
};
// <- 响应：从 choices[0].message 取 content / tool_calls
// 若有工具结果回填：追加 {role:"tool", tool_call_id, content: result}
```

（字段取值与工具规范见 OpenAI 文档）([OpenAI 平台][6])

**Anthropic 适配（核心片段）**

```ts
// -> 请求：顶层 system；messages 只允许 user/assistant；tools 顶层；max_tokens 必填
const toAnthropicMsgs = (msgs: Msg[]) =>
  msgs.map((m) => ({
    role: m.role,
    content: [{ type: "text", text: m.content }],
  }));

const payload = {
  model: "claude-3-5-sonnet-20240620",
  system,
  max_tokens: maxTokens,
  messages: toAnthropicMsgs(messages),
  tools: tools?.map((t) => ({
    name: t.name,
    description: t.description,
    input_schema: t.parameters,
  })),
};
// <- 响应：扫描 content[] 找 {type:"text"} 聚合文本；找 {type:"tool_use"} 产出 ToolCall[]
// 回填工具结果：在下一次请求中，追加一条 user 消息，其 content 包含
// {type:"tool_result", tool_use_id: <id>, content: "执行输出"}
```

（`tool_use/tool_result` 与 `stop_reason` 语义以官方说明为准）([Anthropic][7])

# 十一、流式消费要点

- **OpenAI**：逐 chunk 读取 `choices[].delta`；若出现 `delta.tool_calls`，按 `index` 拼接 `function.arguments`（是**增量字符串**）。工具并行时要以 `index` 归并。([OpenAI 平台][9])
- **Anthropic**：根据事件类型区分文本块与工具块：

  - `content_block_start`（拿到块 `id` 与 `type`），
  - `content_block_delta`（追加文本或 JSON 片段），
  - `message_stop`（可以获取完整 `stop_reason` 与 `usage`）。

# 十二、实务建议与坑位清单

1. **`max_tokens`**：Anthropic 必填；OpenAI 虽可省，但建议显式设置，避免截断与计费不可控。([Anthropic][2], [OpenAI 平台][1])
2. **系统提示位置**：OpenAI 放在 `messages` 数组；Anthropic 放在顶层 `system`。错误放置会被忽略或报错。([Anthropic][2], [OpenAI 平台][1])
3. **图像输入结构**：

   - OpenAI 要用 `content: [{type:"image_url", image_url:{url}}]`；
   - Anthropic 要用 `content: [{type:"image", source:{...}}]` 或 Files API。([OpenAI 平台][3], [Anthropic][5])

4. **工具回填的“角色/类型”**：

   - OpenAI：`role:"tool" + tool_call_id`；
   - Anthropic：`user` 角色消息里嵌 `tool_result` 内容块（务必带 `tool_use_id`）。([OpenAI 平台][6], [Anthropic][7])

5. **并行工具调用**：两家都可能并行；OpenAI 用 `tool_calls[index]`；Anthropic 用多个 `tool_use` 块与各自的 `id`。流式时要按 `index/id` 聚合。([OpenAI 平台][6])
6. **用量统计**：流式通常在尾事件才能拿全量统计；不要指望每个增量都带 usage。([OpenAI 平台][9])

---

如果你正在维护一层“OpenAI 兼容接口”（例如你举的 `api.302.ai/v1/chat/completions`），建议按上面映射做**网关**：

- 将 `system` 从 OpenAI 风格提取到 Anthropic 顶层，
- 将工具定义从 OpenAI `tools[].function.parameters` 映射到 Anthropic `tools[].input_schema`，
- 将 `assistant.tool_calls` ↔ `content[].tool_use` / `tool_result` 做双向转换，
- 流式分发时用事件驱动组装器，分别处理 `delta` 与 `content_block_*`。

参考文档：

- Anthropic Messages API、流式事件与工具调用、愿景/图像示例。([Anthropic][2])
- OpenAI Chat Completions、流式、函数调用与多模态。([OpenAI 平台][1])

需要我把这套适配器拓展成可用的 NPM 包脚手架（含类型与单测）的话，我可以直接给你一版最小可用实现。

[1]: https://platform.openai.com/docs/api-reference/chat/create?utm_source=chatgpt.com "API Reference - OpenAI API"
[2]: https://docs.anthropic.com/en/api/messages?utm_source=chatgpt.com "Messages - Anthropic API"
[3]: https://platform.openai.com/docs/guides/images-vision?utm_source=chatgpt.com "Images and vision - OpenAI API"
[4]: https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/gpt-with-vision?utm_source=chatgpt.com "How to use vision-enabled chat models - Azure OpenAI in Azure AI ..."
[5]: https://docs.anthropic.com/en/docs/build-with-claude/vision?utm_source=chatgpt.com "Vision - Anthropic API"
[6]: https://platform.openai.com/docs/guides/function-calling?utm_source=chatgpt.com "Function calling - OpenAI API"
[7]: https://docs.anthropic.com/en/docs/agents-and-tools/tool-use/overview?utm_source=chatgpt.com "Tool use with Claude - Anthropic API"
[8]: https://docs.aws.amazon.com/bedrock/latest/userguide/model-parameters-anthropic-claude-messages-request-response.html?utm_source=chatgpt.com "Request and Response - Amazon Bedrock - AWS Documentation"
[9]: https://platform.openai.com/docs/api-reference/chat/streaming?utm_source=chatgpt.com "API Reference - OpenAI Platform"
