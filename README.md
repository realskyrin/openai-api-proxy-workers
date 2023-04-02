## 关于

这个部署脚本之前是从别的地方（好像是某个博客）发现的，忘记是来源了，如果有人发现来源可以提 PR 更新下

## Cloudflare Workers 简介

Cloudflare Workers 是一个无服务器平台，使您能够在 Cloudflare 的全球网络上运行代码。作为一项基于服务器的服务，Cloudflare Workers 使开发人员能够编写和部署 JavaScript 代码，使其接近 Cloudflare 全球的边缘节点。开发人员可以使用 Workers 快速构建和启动无服务器应用程序和 API，以及处理请求和响应。Workers 还具有高度可定制的缓存和路由工具，使开发人员可以更好地控制其 Web 应用程序。

## 如何部署

### 1、前置条件
你需要在 Cloudflare 有至少一个域名，可以从别的域名服务商买然后转进去，也可在 Cloudflare 直接注册，可以选 .xyz 这种小众域名比较便宜。

### 2、创建 Workers

登录你的 CF，展开左侧菜单栏中的 Workers -> Overview，创建一个 Service 名字随意
![](/i/e4576578-2600-4af5-bd2b-fdab631e78af.jpg)

在 Triggers Tab 下绑定自己的域名，点击右下角 Add Coustom Domain
![](/i/c7647fc0-7579-4753-b24e-90791bbabe9c.jpg)

添加完之后会告诉你需要等一会，最长 24h，一般用不了这么久
![](/i/cd6b88df-d0df-403b-9c75-cd1cfa9f64a9.jpg)

这个操作会自动在你的 DNS 解析记录里自动增加一条类型为 Worker 的解析记录，并且是 Proxied 状态
![](/i/7800cffc-ac85-45e9-a4f5-7131225c3309.jpg)

点击右上角 Quick edit 编辑 Workers 脚本
![](/i/e7f56f55-d68e-40a1-9fb4-4ee400ce9107.jpg)

复制 [proxy.js](https://raw.githubusercontent.com/realskyrin/openai-api-proxy-workers/main/proxy.js) 的全部内容，填入并 Save and deploy
![](/i/83595d5f-929b-401b-8492-531c63b8ce8f.jpg)

## 测试

现在从浏览器直接访问刚刚绑定的域名，返回如下 Json 就是成功了

```json
{
  "error": {
    "message": "Invalid URL (GET /)",
    "type": "invalid_request_error",
    "param": null,
    "code": null
  }
}
```

在 Terminal 使用 curl 测试

```shell
curl https://<你自己绑定的域名>/v1/chat/completions \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $OPENAI_API_KEY" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

