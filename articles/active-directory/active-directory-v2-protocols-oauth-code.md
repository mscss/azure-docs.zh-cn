
<properties
	pageTitle="应用程序模型 v2.0 OAuth 协议 | Microsoft Azure"
	description="使用 Azure AD 的 OAuth 2.0 身份验证协议实现构建 Web 应用程序。"
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"

	ms.date="01/11/2015"
	wacn.date=""/>

# 应用程序模型 v2.0 预览版：协议 - OAuth 2.0 授权代码流

OAuth 2.0 授权代码授予可用于设备上所安装的应用中，以访问受保护的资源，例如 Web API。通过应用程序模型的 v2.0 实现 OAuth 2.0，可以将登录及 API 访问添加到移动应用程序和桌面应用程序。本指南与语言无关，介绍在不使用我们的任何开放源代码库的情况下，如何发送和接收 HTTP 消息。

<!-- TODO: Need link to libraries -->

> [AZURE.NOTE]
	此信息适用于 2.0 版应用模型的公共预览版。有关如何集成通常可用的 Azure AD 服务的说明，请参阅 [Azure Active Directory 开发人员指南](active-directory-developers-guide.md)。

有关 OAuth 2.0 授权代码流的说明，请参阅 [OAuth 2.0 规范第 4.1 部分](http://tools.ietf.org/html/rfc6749)。在大部分的应用程序类型中，其用于执行身份验证与授权，包括 [Web Apps](active-directory-v2-flows.md#web-apps) 和[本机安装的应用程序](active-directory-v2-flows.md#mobile-and-native-apps)。它可让应用程序安全地获取 access\_tokens，用于访问以 v2.0 应用程序模型保护的资源。



## 请求授权代码
授权代码流始于客户端将用户定向到的 `/authorize` 终结点。在这项请求中，客户端指示必须向用户获取的权限：

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize?client_id=6731de76-14a6-49ae-97bc-6eba6914391e&response_type=code&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&response_mode=query&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&state=12345
```

> [AZURE.TIP] 请尝试将此请求粘贴到浏览器中！

| 参数 | | 说明 |
| ----------------------- | ------------------------------- | --------------- |
| client\_id | 必填 | 注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 分配给应用的应用程序 ID。 |
| response\_type | 必填 | 必须包括授权代码流的 `code`。 |
| redirect\_uri | 建议 | 应用程序的 redirect\_uri，应用程序可在此发送及接收身份验证响应。其必须完全符合在门户中注册的其中一个 redirect\_uris，否则必须是编码的 url。对于本机和移动应用，应使用默认值 `urn:ietf:wg:oauth:2.0:oob`。 |
| 作用域 | 必填 | 你希望用户同意的[范围](active-directory-v2-scopes.md)的空格分隔列表。 |
| response\_mode | 建议 | 指定将生成的令牌送回到应用程序所应该使用的方法。可以是 `query` 或 `form_post`。 |
| state | 建议 | 同样随令牌响应返回的请求中所包含的值。其可以是你想要的任何内容的字符串。随机生成的唯一值通常用于[防止跨站点请求伪造攻击](http://tools.ietf.org/html/rfc6749#section-10.12)。该状态也用于在身份验证请求出现之前，于应用程序中编码用户的状态信息，例如之前所在的网页或视图。 |
| prompt | 可选 | 表示需要的用户交互类型。目前唯一的有效值为“login”、“none”和“consent”。`prompt=login` 强制用户在该请求上输入凭据，否定单一登录。`prompt=none` 则相反 - 它确保不对用户显示任何交互式提示。如果请求无法通过单一登录以无消息方式完成，v2.0 终结点将返回错误。`prompt=consent` 在用户登录之后触发 OAuth 同意对话框，询问用户是否要授予权限给应用程序。 |
| login\_hint | 可选 | 如果事先知道其用户名称，可用于预先填充用户登录页面的用户名称/电子邮件地址字段。通常应用在重新身份验证期间使用此参数，已经使用 `preferred_username` 声明从上一个登录撷使用者户名称。 |
| domain\_hint | 可选 | 可以是 `consumers` 或 `organizations`。如果包含，它跳过用户在 v2.0 登录页面上经历的基于电子邮件的发现过程，导致稍微更加流畅的用户体验。通常应用在重新身份验证期间使用此参数，方法是从前次登录提取 `tid`。如果 `tid` 声明值是 `9188040d-6c67-4c5b-b112-36a304b66dad`，应该使用 `domain_hint=consumers`。否则使用 `domain_hint=organizations`。 |

此时，请求用户输入其凭据并完成身份验证。v2.0 终结点也确保用户已经同意 `scope` 查询参数所示的权限。如果用户未曾同意这些权限的任何一项，就请求用户同意请求的权限。[此处提供了权限、同意与多租户应用程序](active-directory-v2-scopes.md)的详细信息。

用户经过身份验证并同意后，v2.0 终结点将使用 `response_mode` 参数中指定的方法，将响应返回到位于指定所在 `redirect_uri` 的应用程序。

#### 成功的响应
使用 `response_mode=query` 的成功响应如下所示：

```
GET urn:ietf:wg:oauth:2.0:oob?
code=AwABAAAAvPM1KaPlrEqdFSBzjqfTGBCmLdgfSTLEMPGYuNHSUYBrq...
&state=12345
```

| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| 代码 | 应用程序请求的 authorization\_code。应用程序可以使用授权代码请求目标资源的访问令牌。Authorization\_codes 的生存期很短，通常约 10 分钟后即过期。 |
| state | 如果请求中包含状态参数，响应中就应该出现相同的值。应用程序应该验证请求和响应中的状态值是否完全相同。 |

#### 错误响应
错误响应可能也发送到 `redirect_uri`，让应用程序可以适当地处理：

```
GET urn:ietf:wg:oauth:2.0:oob?
error=access_denied
&error_description=the+user+canceled+the+authentication
```

| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| error | 用于分类发生的错误类型与响应错误的错误码字符串。 |
| error\_description | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |

## 请求访问令牌
既然你已获取 authorization\_code 并获得用户授权，现在可以将 `POST` 请求发送到 `/token` 终结点，兑换 `code` 以获取所需资源的 `access_token`：

```
// Line breaks for legibility only

POST /common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh    // NOTE: Only required for web apps
```

> [AZURE.TIP] 尝试将以下 curl 命令导入 postman！（必须以自己的代码替换 `code` 才能成功导入）

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=authorization_code' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 参数 | | 说明 |
| ----------------------- | ------------------------------- | --------------------- |
| client\_id | 必填 | 注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 分配给应用的应用程序 ID。 |
| grant\_type | 必填 | 必须是授权代码流的 `authorization_code`。 |
| 作用域 | 必填 | 范围的空格分隔列表。在此阶段中请求的范围必须相当于或为第一个阶段中所请求的范围子集。如果这个请求中指定的范围遍及多个资源服务器，v2.0 终结点将返回第一个范围内所指定资源的令牌。有关范围的详细说明，请参阅[权限、同意和范围](active-directory-v2-scopes.md)。 |
| 代码 | 必填 | 在流的第一个阶段中获取的 authorization\_code。 |
| redirect\_uri | 必填 | 用于获取 authorization\_code 的相同 redirect\_uri 值。 |
| client\_secret | Web Apps 所需 | 在应用程序注册门户中为应用程序创建的应用程序机密。其不应用于本机应用程序，因为设备无法可靠地存储 client\_secrets。Web Apps 和 Web API 都需要应用程序机密，能够将 client\_secret 安全地存储在服务器端。 |

#### 成功的响应
成功的令牌响应如下：

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| access\_token | 请求的访问令牌。应用程序可以使用此令牌来验证受保护的资源，例如 Web API。 |
| token\_type | 指示令牌类型值。Azure AD 唯一支持的类型是 Bearer |
| expires\_in | 访问令牌的有效期（以秒为单位）。 |
| 作用域 | access\_token 有效的范围。 |
| refresh\_token | OAuth 2.0 刷新令牌。应用程序可以使用此令牌，在当前的访问令牌过期之后获取其他访问令牌。Refresh\_tokens 的生存期很长，而且可以用于延长保留资源访问权限的时间。有关详细信息，请参阅 [v2.0 令牌参考](active-directory-v2-tokens.md)。 |
| id\_token | 无符号 JSON Web 令牌 (JWT)。应用程序可以 base64Url 解码此令牌的段，以请求已登录用户的相关信息。应用程序可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。有关有关 id\_tokens 的详细信息，请参阅 [v2.0 应用程序模型令牌参考](active-directory-v2-tokens.md)。 |

#### 错误响应
错误响应如下所示：

```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| error | 用于分类发生的错误类型与响应错误的错误码字符串。 |
| error\_description | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| error\_codes | 帮助诊断的 STS 特定错误代码列表。 |
| timestamp | 发生错误的时间。 |
| trace\_id | 帮助诊断的请求唯一标识符。 |
| correlation\_id | 帮助跨组件诊断的请求唯一标识符。 |

## 使用访问令牌
成功获取 `access_token` 后，可以通过在 `Authorization` 标头中包含令牌，在 Web API 的请求中使用令牌：

```
GET /v1.0/me/messages
Host: https://graph.microsoft.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...
```

> [AZURE.TIP] 请尝试发出以下命令！（但请替换为你自己的令牌）

```
curl -X GET -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q" 'https://graph.microsoft.com/v1.0/me/messages'
```

## 刷新访问令牌
Access\_token 生存期很短，必须在其过期后刷新，才能继续访问资源。为此，你可以向 `/token` 终结点提交另一个 `POST` 请求，但这次要提供 `refresh_token` 而不是 `code`：

```
// Line breaks for legibility only

POST /common/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read
&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=refresh_token
&client_secret=JqQX2PNo9bpM0uEihUPzyrh	  // NOTE: Only required for web apps
```

> [AZURE.TIP] 尝试将以下 curl 命令导入 postman！（必须以自己的代码替换 refresh\_token 才能成功导入）

```
curl -X POST -H "Cache-Control: no-cache" -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=6731de76-14a6-49ae-97bc-6eba6914391e&scope=https%3A%2F%2Fgraph.microsoft.com%2Fmail.read&refresh_token=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXrFgnryzZvcDbKTvyz36ono600tLhxSdnoOe50zSgxiIQhD36sIPLln7lNOMrUi1ralV_hOfZItjuwqeTOTFgXRG_rhkIzBfKmudQHD1KUodPD84a308LAfJ5ciLak9nlNVyVOL7gViWADpdZv_KrBXgaJXkxKZ4qxeYT_wf6yajHP2Gt4LPijuhqJIsqId7Xo8FkNIsmlvZkdArZDLgpZdunDmnis_623fu4vMeuWyVhrAoesilIqbwP_bKWNhGO_fcQ1Spsa-TDgfqUyrXnk3UYc-B3m6Npvkx3bYv3NrUSNxqdMONxR-3HowU3Uke-jM3Z8GR25HE4YAdfTqVxHtd6DEP9aamMIRH0LwuM4uxUrgeALqpbPenabekOZkkZ5-KKY4AyJKMOWxvMmqJRz9gYHnGUxqKcl2-F7250rHNGZTbJPurie_3WzNrRKFOQAF84mbsGoeYvSXlbI5uiH3Bw9kpOw302r26K4j-IKoMpw2BXU0mNxoGEL_wC0oTkVqRNg_sTTcsAPU1giW0hj-LONWc0ZgcKNI00fXaC5l6V8i2ERWyBy4Ys8gKIc7mynZnCpf2tgrxMBH5sloZ1Lf6P63CiAA&client_secret=JqQX2PNo9bpM0uEihUPzyrh&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F&grant_type=refresh_token' 'https://login.microsoftonline.com/common/oauth2/v2.0/token'
```

| 参数 | | 说明 |
| ----------------------- | ------------------------------- | -------- |
| client\_id | 必填 | 注册门户 ([apps.dev.microsoft.com](https://apps.dev.microsoft.com)) 分配给应用的应用程序 ID。 |
| grant\_type | 必填 | 必须是授权代码流的此阶段的 `refresh_token`。 |
| 作用域 | 必填 | 范围的空格分隔列表。在此阶段请求的范围必须等效于或者为原始 authorization\_code 请求阶段中所请求的范围子集。如果这个请求中指定的范围遍及多个资源服务器，v2.0 终结点将返回第一个范围内所指定资源的令牌。有关范围的详细说明，请参阅[权限、同意和范围](active-directory-v2-scopes.md)。 |
| refresh\_token | 必填 | 在流的第二个阶段获取的 refresh\_token。 |
| redirect\_uri | 必填 | 用于获取 authorization\_code 的相同 redirect\_uri 值。 |
| client\_secret | Web Apps 所需 | 在应用程序注册门户中为应用程序创建的应用程序机密。其不应用于本机应用程序，因为设备无法可靠地存储 client\_secrets。Web Apps 和 Web API 都需要应用程序机密，能够将 client\_secret 安全地存储在服务器端。 |

#### 成功的响应
成功的令牌响应如下：

```
{
	"access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
	"token_type": "Bearer",
	"expires_in": "3600",
	"scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
	"refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
	"id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| access\_token | 请求的访问令牌。应用程序可以使用此令牌来验证受保护的资源，例如 Web API。 |
| token\_type | 指示令牌类型值。Azure AD 唯一支持的类型是 Bearer |
| expires\_in | 访问令牌的有效期（以秒为单位）。 |
| 作用域 | access\_token 有效的范围。 |
| refresh\_token | 新的 OAuth 2.0 刷新令牌。应该将旧刷新令牌替换为新获取的这个刷新令牌，以确保刷新令牌的有效期尽可能地长。 |
| id\_token | 无符号 JSON Web 令牌 (JWT)。应用程序可以 base64Url 解码此令牌的段，以请求已登录用户的相关信息。应用程序可以缓存并显示值，但不应依赖于这些值来获取任何授权或安全边界。有关有关 id\_tokens 的详细信息，请参阅 [v2.0 应用程序模型令牌参考](active-directory-v2-tokens.md)。 |

#### 错误响应
```
{
  "error": "invalid_scope",
  "error_description": "AADSTS70011: The provided value for the input parameter 'scope' is not valid. The scope https://foo.microsoft.com/mail.read is not valid.\r\nTrace ID: 255d1aef-8c98-452f-ac51-23d051240864\r\nCorrelation ID: fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7\r\nTimestamp: 2016-01-09 02:02:12Z",
  "error_codes": [
    70011
  ],
  "timestamp": "2016-01-09 02:02:12Z",
  "trace_id": "255d1aef-8c98-452f-ac51-23d051240864",
  "correlation_id": "fb3d2015-bc17-4bb9-bb85-30c5cf1aaaa7"
}
```

| 参数 | 说明 |
| ----------------------- | ------------------------------- |
| error | 用于分类发生的错误类型与响应错误的错误码字符串。 |
| error\_description | 帮助开发人员识别身份验证错误根本原因的特定错误消息。 |
| error\_codes | 帮助诊断的 STS 特定错误代码列表。 |
| timestamp | 发生错误的时间。 |
| trace\_id | 帮助诊断的请求唯一标识符。 |
| correlation\_id | 帮助跨组件诊断的请求唯一标识符。 |

## 摘要
从较高层面讲，本机/移动应用程序的整个身份验证流有点类似于：

![OAuth 授权代码流](../media/active-directory-v2-flows/convergence_scenarios_native.png)

<!---HONumber=Mooncake_0321_2016-->