# API 文档

## 1. AI Chat

**Endpoint**: `POST {service-url}/api/chat`

**Description**: AI chat functionality. AI 聊天功能

**Request Headers**:

| Parameter Name | Type   | Description       | Required |
| -------------- | ------ | ----------------- | -------- |
| Authorization  | Bearer | token             | 必填     |
| accept         |        | text/event-stream | 可选     |

**Request Body**:

| Parameter Name | Type         | Description              | Required |
| -------------- | ------------ | ------------------------ | -------- |
| question       | string       | 提问                     | 必填     |
| uid            | UUID         | 用户id，用户自行生成设置 | 必填     |
| history        | array string | 历史聊天记录             | 可选     |
| qa_prompt      | string       | 提示语                   | 可选     |
| temperature    | number       | 热度 0~1，用于调试回答的灵活性                 | 可选     |

```json
{
  "question": "xxxx?", 
  "history": [["问1xxx？","回答1"],["问2xxx？","回答2"]], 
  "qa_prompt": "As a chat assistent, can you help me: xxxx", 
  "temperature": 0,
  "uid": "[UUID]"
}
```

**Response SSE body**:

在输出信息来源的JSON中新加字段 `chat_id` : 用于标记对话 id 这个参数可以用于feedback 来提升语料质量。

data: {"sourceDocs":[{"pageContent"................................"}}],"chat_id":"71746553-1652-4a22-833b-f45e798aa1d1"}

对协议进行如下拓展：

data {"action":"RETRYING"}
如果系统在SSE中发送该数据包，说明系统内部进行了重试， LLM之前回答一半的内容作废。
回答一半的定义： 从消息最开始或从上一次BREAK开始计算。

data {"action":"BREAK"}
代表本次AI回答可以从这里断开， 分多条消息回答，用来应对 wechat 等无法高频编辑历史消息的软件导致需要等超级久才能回复的问题。


## 2. Ping

**Endpoint**: `GET {service-url}/api/ping`

**Description**: Test the API to check if the service is running. 测试服务是否在运行


## 3. Feedback

**Endpoint**: `POST {service-url}/api/feedback`

**Description**: User feedback function. 用户反馈功能

**Request Headers**:

| Parameter Name | Type   | Description       | Required |
| -------------- | ------ | ----------------- | -------- |
| Authorization  | Bearer | token             | 必填     |
| accept         |        | text/event-stream | 可选     |

**Request Body**:

| Parameter Name | Type   | Description                                | Required |
| -------------- | ------ | ------------------------------------------ | -------- |
| chat_id        | string | UUID, 跟/api/chat 接口一样                 | 必填     |
| uid            | string | 用户id                                     | 必填     |
| stars          | number | [1, 2, 3, 4, 5] 用户评价对话，5最好，1平庸 | 选填     |
| reason         | string | 反馈原因                                   | 选填     |
| better_answer  | string | 更好的回答                                 | 选填     |

```json
{
    "chat_id":"8ab304d4-2bb8-4843-8b7f-4eff5a1d1f77",
    "uid":"baa9f7a8-4159-4e2c-89d2-e7c5ac537935",
    "stars":1,
    "reason":"材料错误"
}
```
