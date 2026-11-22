# 微博数据接口（Weibo API）：微博搜索、评论、用户、热搜

**微博数据 API · 微博搜索 · 微博评论采集 · 微博用户主页 · 微博热搜榜。**

这个仓库是 [okdata](https://okdata.online/zh/?src=gh-weibo-api) 的微博接口示例：11 个接口在线可用，一个 API Key 调用，按次计费（$0.002 起），不用订阅。不用登录平台账号，不用 Cookie，不开浏览器，所以也不存在账号被风控的问题。

[免费注册拿 Key（送 $0.1 试用额度）](https://okdata.online/zh/signup/?src=gh-weibo-api) · [全部接口文档](https://okdata.online/zh/apis/?src=gh-weibo-api) · [接入 AI（MCP）](https://okdata.online/zh/mcp/?src=gh-weibo-api)

## 接口清单

| 接口 | 说明 | 价格 | 路径 |
|---|---|---|---|
| [微博搜索](https://okdata.online/zh/apis/weibo/posts/search/?src=gh-weibo-api) | 按关键词搜微博，可按实时或热门，带转发、评论和点赞。 | $0.002 / 次 | `GET /v1/weibo/posts/search` |
| [微博热搜](https://okdata.online/zh/apis/weibo/trends/list/?src=gh-weibo-api) | 微博热搜榜，带排名和热度。 | $0.002 / 次 | `GET /v1/weibo/trends/list` |
| [微博账号信息](https://okdata.online/zh/apis/weibo/users/get/?src=gh-weibo-api) | 账号的粉丝数、微博数、认证和简介。 | $0.002 / 次 | `GET /v1/weibo/users/get` |
| [微博详情](https://okdata.online/zh/apis/weibo/posts/get/?src=gh-weibo-api) | 按 ID 或链接取一条微博：全文（长微博也是全文）、作者、点赞、评论、转发、阅读数（视频微博是播放数）、图片、话题和 IP 属地。 | $0.002 / 次 | `GET /v1/weibo/posts/get` |
| [微博评论](https://okdata.online/zh/apis/weibo/posts/comments/list/?src=gh-weibo-api) | 按 ID 或链接取微博评论，按热度排：内容、评论者、点赞、回复数、发布时间和 IP 属地。 | $0.002 / 次 | `GET /v1/weibo/posts/comments/list` |
| [微博评论回复](https://okdata.online/zh/apis/weibo/comments/replies/list/?src=gh-weibo-api) | 按评论 ID 取它下面的回复：内容、回复者、点赞、回复的是哪一条（parent_id）、发布时间和 IP 属地。 | $0.002 / 次 | `GET /v1/weibo/comments/replies/list` |
| [微博点赞用户](https://okdata.online/zh/apis/weibo/posts/likers/list/?src=gh-weibo-api) | 按 ID 或链接取最近给这条微博点赞的账号，约 50 个：昵称、简介、头像、认证和认证类型、粉丝、关注、微博数，以及资料里填的性别和地区。 | $0.002 / 次 | `GET /v1/weibo/posts/likers/list` |
| [微博用户微博](https://okdata.online/zh/apis/weibo/users/posts/list/?src=gh-weibo-api) | 按用户 ID 取账号主页上的微博（原创和转发），从新到旧：正文（长微博在这里是截短的，ext.text_truncated 标明）、点赞、评论、转发、视频播放数、图片和话题。 | $0.002 / 次 | `GET /v1/weibo/users/posts/list` |
| [微博用户粉丝列表](https://okdata.online/zh/apis/weibo/users/followers/list/?src=gh-weibo-api) | 关注这个用户的账号，每页 20 个：昵称、简介、头像、认证和认证类型、粉丝、关注、微博数、注册时间，以及资料里填的性别和地区。微博只给前几页。 | $0.002 / 次 | `GET /v1/weibo/users/followers/list` |
| [微博用户搜索](https://okdata.online/zh/apis/weibo/users/search/?src=gh-weibo-api) | 按关键词搜微博账号：用户 ID、昵称、头像和主页链接。 | $0.002 / 次 | `GET /v1/weibo/users/search` |
| [微博热搜榜（备用版）](https://okdata.online/zh/apis/weibo/backup/trends/list/?src=gh-weibo-api) | 微博热搜榜（weibo.trends.list）的备用版，原接口取不到时改调：取微博热搜（App 端），价格略高。50 个上榜话题、名次和热度；不含置顶话题和推广位。 | $0.0025 / 次 | `GET /v1/weibo/backup/trends/list` |

## 快速开始

1. 在 [okdata 控制台](https://okdata.online/zh/signup/?src=gh-weibo-api) 用邮箱注册，拿到 `okd_` 开头的 Key。
2. 调一次接口：

```bash
export OKDATA_API_KEY=okd_...
curl -G "https://api.okdata.online/v1/weibo/posts/search" \
  -H "Authorization: Bearer $OKDATA_API_KEY" \
  --data-urlencode 'query=新品发布'
```

Python 版在 [examples/quickstart.py](examples/quickstart.py)（只用标准库，会顺带演示翻页）：`python3 examples/quickstart.py`。

返回统一的结构：列表在 `data.items`，下一页用 `page.next_cursor` 传回 `cursor`，这次扣了多少钱在 `meta.billing.amount`，平台不提供的字段为 null 并在 `meta.unavailable` 里写明原因。

热搜榜不用翻页，一条命令取 50 条：

```bash
curl -G "https://api.okdata.online/v1/weibo/trends/list" \
  -H "Authorization: Bearer $OKDATA_API_KEY"
```

### 真实输出（2026-11-22 实跑）

上面这条命令当天取回 50 个上榜话题，前三条是：

```console
1  主播私闯果园带货糟蹋一千多斤苹果  2,516,054
2  女博士卧底当主播称至今心理阴影      998,475
3  温差待消弭信心不可失              798,218
花费 $0.002
```

完整响应在 [examples/weibo.trends.list.json](examples/weibo.trends.list.json)（50 条，含每条的 `rank`、`name`、`heat` 和微博搜索链接）。热搜会变，榜单以你调用时为准。

## 让 AI 直接用（MCP）

Claude Code：

```bash
claude mcp add --transport http --scope user okdata https://api.okdata.online/mcp --header "Authorization: Bearer $OKDATA_API_KEY"
```

Cursor、Cherry Studio、WorkBuddy 等支持远程 MCP 的客户端：

```json
{
  "mcpServers": {
    "okdata": {
      "url": "https://api.okdata.online/mcp",
      "headers": {
        "Authorization": "Bearer okd_..."
      }
    }
  }
}
```

接好以后直接用中文说需求，比如「微博搜索：新品发布」。AI 会先用免费的 `find`、`describe` 找接口和看价格，再用 `call` 取数据。

## 常见问题

**要登录平台账号吗？** 不用。数据是公开内容，okdata 在服务端获取，你的账号完全不参与。

**怎么收费？** 每个接口单独定价，表里写的就是一次调用的价格；按条计费的接口按实际返回条数算。新账号送 $0.1 试用额度，之后 $10 起充，余额不过期，没有订阅。价格页：https://okdata.online/zh/pricing/?src=gh-weibo-api ，计费说明：https://okdata.online/zh/docs/billing/?src=gh-weibo-api

**和官方开放平台有什么区别？** 官方开放平台只能读授权给你的账号自己的数据，而且要企业资质和审核；这里是平台上任何人都能看到的公开数据，比如搜索结果、别人的笔记或视频、评论、热榜。

**能翻页吗？** 列表接口每页一次调用，把 `page.next_cursor` 原样传回 `cursor` 就是下一页，24 小时内有效。

**出错了怎么办？** 每个错误都有固定的 `code`、中文说明 `message_zh`，以及会不会扣费（`charged`）和要不要重试（`retry`）。全部错误码：https://okdata.online/zh/docs/errors/?src=gh-weibo-api

## English

Examples for the okdata Weibo API: 11 live endpoints behind one API key, pay per call from $0.002, no platform login or cookies. Sign up at https://okdata.online/signup/?src=gh-weibo-api, docs at https://okdata.online/apis/?src=gh-weibo-api, MCP at https://okdata.online/mcp/?src=gh-weibo-api.

## License

MIT. The data comes from the okdata API; its terms apply to API use.
