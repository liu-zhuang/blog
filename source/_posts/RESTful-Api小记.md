---
title: RESTful Api小记
date: 2016-08-25 22:38:29
tags: RESTful API 
---
# 何为RESTFUL
REST是[Representational State Transfer](https://en.wikipedia.org/wiki/Representational_state_transfer)的缩写，翻译为中文就是：表现层状态转化。
REST这个词是由[Roy Fielding](https://en.wikipedia.org/wiki/Roy_Fielding)在2000年的[博士论文](http://www.ics.uci.edu/~fielding/pubs/dissertation/top.htm)中首次提出的。
Fielding将他对互联网软件的架构原则定义为:REST，如果一个架构符合REST原则，就称为RESTFUL架构。

# 如何理解REST
要理解REST也就是`Representational State Transfer`，首先要理解一个概念：Resources,也就是资源，
在REST这个短语中，其实省略了主语：Resources,表现层其实指的是资源的表现层。
所谓的资源，就是网络上的一个实体，它可以是一段文本，一首歌曲，一张图片，一个服务，总之是一种真实的存在，可以用URL指向它，每种资源都有自己独一无二的URL，要获取这个资源，访问它的URL即可。
接下来说说`Representational`,因为上面说了，资源是一种实体，它可以有多种外在表现形式，我们把资源表现出来的形式，称之为“表现层“。
比如一段文本，可以以txt格式显示，也可以以html或者xml，json等多种格式显示。
它具体的表现形式，应该在报文头中用`Accept`和`Content-Type`指定，这两个字段是对“表现层“的描述。eg:
``` http
accept: application/json
accept-encoding: gzip, deflate
accept-language: en-US,en;q=0.8
content-type: application/json
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/56.0.2924.87 Safari/537.36
```
最后就是状态转化了，访问一个网站，就代表了客户端和服务器的一个互动过程。在这个过程中，势必涉及到数据和状态的变化。
互联网通信协议HTTP协议，是一个无状态协议。这意味着，所有的状态都保存在服务器端。因此，如果客户端想要操作服务器，必须通过某种手段，让服务器端发生"状态转化"（State Transfer）。而这种转化是建立在表现层之上的，所以就是"表现层状态转化"。
客户端用到的手段，只能是HTTP协议。
综上所述：所谓的RESTful架构需要满足以下三点:
- 每个url代表一种资源
- 客户端和服务器之间传递这种资源的某种表现形式
- 客户端通过http动词，对服务器上的某种资源进行操作，以实现“表现层状态转化”

# 如何设计RESTful API
参考文章:
- [Principles of good RESTful API Design](https://codeplanet.io/principles-good-restful-api-design/)
- [REST best practices](https://bourgeois.me/rest/)
- [RESTful API 设计指南](http://www.ruanyifeng.com/blog/2014/05/restful_api.html)
主要是学习了阮一峰老师的这篇博客的小结。

## 一、协议
API与用户的通信协议，总是使用HTTPs协议。

## 二、域名
应该尽量将api部署在专门的域名下：
``` 
https://api.example.com
```
如果api很简单，不需要考虑其扩展性，也可以放在主域名下：
```
https://example.com/api/
```

## 三、版本
处理版本号的方式有两种：
- 将版本号放在放入url:
``` 
https://api.example/com/v1/
```
- 将版本号放在HTTP报文头中，但是不如放在url中只管和方便。[Github](https://developer.github.com/v3/media/#request-specific-version)是采用这种方式的。
如下图所示：
![github api 头信息](github-api-2.png)
使用`curl https://api.github.com/users/whatever -i`命令查看某一个github提供的api，可以从图上看到有这样一行：`X-GitHub-Media-Type: github.v3; format=json`，（以X-开头的都是自定义头，并不包含于HTTP标准），用以描述当前api的版本信息。

## 四、路径
路径又称为`终点(endpoint)`,(配置过webservice和wcf表示这个词好熟悉-。-)，表示API的具体网址。
在RESTful架构中，每个网址代表一种资源（resource），所以网址中不能有动词，只能有名词，而且所用的名词往往与数据库的表格名对应。一般来说，数据库中的表都是同种记录的"集合"（collection），所以API中的名词也应该使用复数。
举例来说，有一个API提供动物园（zoo）的信息，还包括各种动物和雇员的信息，则它的路径应该设计成下面这样。
``` 
https://api.example.com/v1/zoos
https://api.example.com/v1/animals
https://api.example.com/v1/employees
```

## 五、HTTP动词
对于资源的具体操作类型，由HTTP动词表示。
常见的命令如下：（（括号里是对应的SQL命令）
``` 
GET（SELECT）：从服务器取出资源（一项或多项）
POST（CREATE）：在服务器新建一个资源。
PUT（UPDATE）：在服务器更新资源（客户端提供改变后的完整资源）
PATCH（UPDATE）：在服务器更新资源（客户端提供改变的属性）
DELETE（DELETE）：从服务器删除资源
```
还有两个不常用的HTTP动词：
```
HEAD：获取资源的元数据
OPTIONS：获取信息，关于资源的哪些属性是客户端可以改变的
```
以下是一些示例：
```
GET /zoos：列出所有动物园
POST /zoos：新建一个动物园
GET /zoos/ID：获取某个指定动物园的信息
PUT /zoos/ID：更新某个指定动物园的信息（提供该动物园的全部信息）
PATCH /zoos/ID：更新某个指定动物园的信息（提供该动物园的部分信息）
DELETE /zoos/ID：删除某个动物园
GET /zoos/ID/animals：列出某个指定动物园的所有动物
DELETE /zoos/ID/animals/ID：删除某个指定动物园的指定动物
```

## 六、过滤信息
如果记录数量很多，服务器不可能都将它们返回给用户。API应该提供参数，过滤返回结果。
以下是一些常见的过滤参数示例：
```
?limit=10：指定返回记录的数量
?offset=10：指定返回记录的开始位置。
?page=2&per_page=100：指定第几页，以及每页的记录数。
?sortby=name&order=asc：指定返回结果按照哪个属性排序，以及排序顺序。
?animal_type_id=1：指定筛选条件
```
参数的设计允许存在冗余，即允许API路径和URL参数偶尔有重复。比如，GET /zoo/ID/animals 与 GET /animals?zoo_id=ID 的含义是相同的。

## 七、状态码
服务器向用户返回的状态码和提示信息，常见的有以下一些（方括号中是该状态码对应的HTTP动词）。
```
200 OK - [GET]：服务器成功返回用户请求的数据，该操作是幂等的（Idempotent）。
201 CREATED - [POST/PUT/PATCH]：用户新建或修改数据成功。
202 Accepted - [*]：表示一个请求已经进入后台排队（异步任务）
204 NO CONTENT - [DELETE]：用户删除数据成功。
400 INVALID REQUEST - [POST/PUT/PATCH]：用户发出的请求有错误，服务器没有进行新建或修改数据的操作，该操作是幂等的。
401 Unauthorized - [*]：表示用户没有权限（令牌、用户名、密码错误）。
403 Forbidden - [*] 表示用户得到授权（与401错误相对），但是访问是被禁止的。
404 NOT FOUND - [*]：用户发出的请求针对的是不存在的记录，服务器没有进行操作，该操作是幂等的。
406 Not Acceptable - [GET]：用户请求的格式不可得（比如用户请求JSON格式，但是只有XML格式）。
410 Gone -[GET]：用户请求的资源被永久删除，且不会再得到的。
422 Unprocesable entity - [POST/PUT/PATCH] 当创建一个对象时，发生一个验证错误。
500 INTERNAL SERVER ERROR - [*]：服务器发生错误，用户将无法判断发出的请求是否成功。
```
完整的状态码戳[这里](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)
中文的见这里：(摘自网络，仅供参考)
```
Informational 1xx
表示临时响应并需要请求者继续执行操作的状态代码。
100 Continue
（继续）说明部分请求已成功收到，其余的现在应该发出。
101 Switching Protocols
（切换协议） 请求者已要求服务器切换协议，服务器已确认并准备切换。

Successful 2xx
表示成功处理了请求的状态代码
200 OK
表示成功处理了请求的状态代码。
201 Created
（已创建） 请求成功并且服务器创建了新的资源。
202 Accepted
（已接受） 服务器已接受请求，但尚未处理。
203 Non-Authoritative Information
（非授权信息） 服务器已成功处理了请求，但返回的信息可能来自另一来源。
204 No Content
（无内容） 服务器成功处理了请求，但没有返回任何内容。
205 Reset Content
（重置内容） 服务器成功处理了请求，但没有返回任何内容。 与 204 响应不同，此响应要求请求者重置文档视图（例如，清除表单内容以输入新内容）。
206 Partial Content
服务器成功处理了部分 GET 请求。

Redirection 3xx
要完成请求，需要进一步操作。 通常，这些状态代码用来重定向。 Google 建议您在每次请求中使用重定向不要超过 5 次。 您可以使用网站管理员工 具查看一下 Googlebot 在抓取重定向网页时是否遇到问题。 诊断下的网络抓取页中列出了由于重定向错误而导致 Googlebot 无法抓取的 网址。
300 Multiple Choices
（多种选择） 针对请求，服务器可执行多种操作。 服务器可根据请求者 (user agent) 选择一项操作，或提供操作列表供请求者选择。
301 Moved Permanently
（永久移动） 请求的网页已永久移动到新位置。 服务器返回此响应（对 GET 或 HEAD 请求的响应）时，会自动将请求者转到新位置。
302 Found
（临时移动） 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。 此代码与响应 GET 或 HEAD 请求的 301 代码类似，会自动将请求者转到不同的位置。
303 See Other
（查看其他位置） 请求者应当对不同的位置使用单独的 GET 请求来检索响应时，服务器返回此代码。 对于除 HEAD 之外的所有请求，服务器会自动转到其他位置。
304 Not Modified
（未修改） 自从上次请求后，请求的网页未修改过。 服务器返回此响应时，不会返回网页内容。 如果网页自请求者上次请求后再也没有更改过，您应当将服务器配置为返回此响应（称为 If-Modified-Since HTTP 标头）。
305 Use Proxy
（使用代理） 请求者只能使用代理访问请求的网页。 如果服务器返回此响应，还表示请求者应使用代理。
306 (Unused)
307 Temporary Redirect
（临时重定向） 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。 此代码与响应 GET 和 HEAD 请求的 301 代码类似，会自动将请求者转到不同的位置。

Client Error 4xx
这些状态代码表示请求可能出错，妨碍了服务器的处理。
400 Bad Request
（错误请求） 服务器不理解请求的语法。
401 Unauthorized
（未授权） 请求要求身份验证。 对于需要登录的网页，服务器可能返回此响应。
402 Payment Required
未被使用的代码，被标识为“保留，以备经来使用”
403 Forbidden
（禁止） 服务器拒绝请求。
404 Not Found
（未找到） 服务器找不到请求的网页。
405 Method Not Allowed
（方法禁用） 禁用请求中指定的方法。
406 Not Acceptable
（不接受） 无法使用请求的内容特性响应请求的网页
407 Proxy Authentication Required
（需要代理授权） 此状态代码与 401（未授权）类似，但指定请求者应当授权使用代理。 如果服务器返回此响应，还会指明请求者应当使用的代理。
408 Request Timeout
（请求超时） 服务器等候请求时发生超时。
409 Conflict
（冲突） 服务器在完成请求时发生冲突。 服务器必须在响应中包含有关冲突的信息。 服务器在响应与前一个请求相冲突的 PUT 请求时可能会返回此代码，同时会附上两个请求的差异列表。
410 Gone
（已删除） 如果请求的资源已永久删除，服务器就会返回此响应。 该代码与 404（未找到）代码相似，但在资源以前存在而现在不存在的情况下，有时会用来替代 404 代码。 如果资源已永久删除，您应当使用 301 指定资源的新位置。
411 Length Required
（需要有效长度） 服务器不接受不含有效内容长度标头字段的请求。
412 Precondition Failed
（未满足前提条件） 服务器未满足请求者在请求中设置的其中一个前提条件。
413 Request Entity Too Large
（请求实体过大） 服务器无法处理请求，因为请求实体过大，超出服务器的处理能力。
414 Request-URI Too Long
（不支持的媒体类型） 请求的格式不受请求页面的支持。
415 Unsupported Media Type
（不支持的媒体类型） 请求的格式不受请求页面的支持。
416 Requested Range Not Satisfiable
（请求范围不符合要求） 如果页面无法提供请求的范围，则服务器会返回此状态代码。
417 Expectation Failed
（未满足期望值） 服务器未满足"期望"请求标头字段的要求。

Server Error 5xx
这些状态代码表示服务器在尝试处理请求时发生内部错误。 这些错误可能是服务器本身的错误，而不是请求出错
500 Internal Server Error
（服务器内部错误） 服务器遇到错误，无法完成请求。
501 Not Implemented
（尚未实施） 服务器不具备完成请求的功能。 例如，服务器无法识别请求方法时可能会返回此代码。
502 Bad Gateway
（错误网关） 服务器作为网关或代理，从上游服务器收到无效响应。
503 Service Unavailable
（服务不可用） 服务器目前无法使用（由于超载或停机维护）。 通常，这只是暂时状态。
504 Gateway Timeout
（网关超时） 服务器作为网关或代理，但是没有及时从上游服务器收到请求。
505 HTTP Version Not Supported
（HTTP 版本不受支持） 服务器不支持请求中所用的 HTTP 协议版本。
```
## 八、错误处理
如果状态码是4xx或者5xx，就应该向用户返回出错信息。一般来说，返回的信息中将error作为键名，出错信息作为键值即可。例如：
```
{
    error: "Invalid API key"
}
```

## 九、返回结果
针对不同操作，服务器向用户返回的结果应该符合以下规范。
``` 
GET /collection：返回资源对象的列表（数组）
GET /collection/resource：返回单个资源对象
POST /collection：返回新生成的资源对象
PUT /collection/resource：返回完整的资源对象
PATCH /collection/resource：返回完整的资源对象
DELETE /collection/resource：返回一个空文档
```

## 十、Hypermedia API（超媒体API）
RESTful API最好做到Hypermedia，即返回结果中提供链接，连向其他API方法，使得用户不查文档，也知道下一步应该做什么。
Hypermedia API的设计被称为[HATEOAS](https://cn.wikipedia.org/wiki/HATEOAS)。
仍然以github为例，访问`https://api.github.com`，得到的内容如下：
``` json
{
  "current_user_url": "https://api.github.com/user",
  "current_user_authorizations_html_url": "https://github.com/settings/connections/applications{/client_id}",
  "authorizations_url": "https://api.github.com/authorizations",
  "code_search_url": "https://api.github.com/search/code?q={query}{&page,per_page,sort,order}",
  "commit_search_url": "https://api.github.com/search/commits?q={query}{&page,per_page,sort,order}",
  "emails_url": "https://api.github.com/user/emails",
  "emojis_url": "https://api.github.com/emojis",
  "events_url": "https://api.github.com/events",
  "feeds_url": "https://api.github.com/feeds",
  "followers_url": "https://api.github.com/user/followers",
  "following_url": "https://api.github.com/user/following{/target}",
  "gists_url": "https://api.github.com/gists{/gist_id}",
  "hub_url": "https://api.github.com/hub",
  "issue_search_url": "https://api.github.com/search/issues?q={query}{&page,per_page,sort,order}",
  "issues_url": "https://api.github.com/issues",
  "keys_url": "https://api.github.com/user/keys",
  "notifications_url": "https://api.github.com/notifications",
  "organization_repositories_url": "https://api.github.com/orgs/{org}/repos{?type,page,per_page,sort}",
  "organization_url": "https://api.github.com/orgs/{org}",
  "public_gists_url": "https://api.github.com/gists/public",
  "rate_limit_url": "https://api.github.com/rate_limit",
  "repository_url": "https://api.github.com/repos/{owner}/{repo}",
  "repository_search_url": "https://api.github.com/search/repositories?q={query}{&page,per_page,sort,order}",
  "current_user_repositories_url": "https://api.github.com/user/repos{?type,page,per_page,sort}",
  "starred_url": "https://api.github.com/user/starred{/owner}{/repo}",
  "starred_gists_url": "https://api.github.com/gists/starred",
  "team_url": "https://api.github.com/teams",
  "user_url": "https://api.github.com/users/{user}",
  "user_organizations_url": "https://api.github.com/user/orgs",
  "user_repositories_url": "https://api.github.com/users/{user}/repos{?type,page,per_page,sort}",
  "user_search_url": "https://api.github.com/search/users?q={query}{&page,per_page,sort,order}"
}
```
从上面可以看到，如果想获取当前用户的信息，应该去访问`api.github.com/user`，然后就得到了下面结果。
``` 
{
  "message": "Not Found",
  "documentation_url": "https://developer.github.com/v3"
}
```
从上面的结果可以看出文档的地址。接下来根据文档的描述，可以利用这个api找到我的信息(羞~(@^_^@)~),访问`api.github.com/user/nicky-lau`，得到以下信息：
```
{
  "login": "nicky-lau",
  "id": 12084271,
  "avatar_url": "https://avatars2.githubusercontent.com/u/12084271?v=3",
  "gravatar_id": "",
  "url": "https://api.github.com/users/nicky-lau",
  "html_url": "https://github.com/nicky-lau",
  "followers_url": "https://api.github.com/users/nicky-lau/followers",
  "following_url": "https://api.github.com/users/nicky-lau/following{/other_user}",
  "gists_url": "https://api.github.com/users/nicky-lau/gists{/gist_id}",
  "starred_url": "https://api.github.com/users/nicky-lau/starred{/owner}{/repo}",
  "subscriptions_url": "https://api.github.com/users/nicky-lau/subscriptions",
  "organizations_url": "https://api.github.com/users/nicky-lau/orgs",
  "repos_url": "https://api.github.com/users/nicky-lau/repos",
  "events_url": "https://api.github.com/users/nicky-lau/events{/privacy}",
  "received_events_url": "https://api.github.com/users/nicky-lau/received_events",
  "type": "User",
  "site_admin": false,
  "name": "liu zhuang",
  "company": null,
  "blog": "http://liu-zhuang.github.io",
  "location": "Shang Hai,China",
  "email": "70458055@qq.com",
  "hireable": null,
  "bio": null,
  "public_repos": 13,
  "public_gists": 0,
  "followers": 4,
  "following": 9,
  "created_at": "2015-04-23T12:36:38Z",
  "updated_at": "2016-07-21T22:56:52Z"
}
```

## 十一、其他
- API的身份认证应该使用OAuth 2.0框架。
- 服务器返回的数据格式，应该尽量使用JSON，避免使用XML。

# 实际应用
参考某MS大叔的博客，地址戳[这里](http://bitoftech.net/2013/11/25/detailed-tutorial-building-asp-net-web-api-restful-service/)
demo戳[这里](https://github.com/tjoudeh/eLearning.WebAPI)

不过今天看到在.net core中新建webapi controller的时候，会默认生成"GET POST PUT DELETE"方法，自己要做的事情就是实现就可以了。
（家里的台式机坏了，在macbook上装windows系统或者虚拟机实在不能忍，等过段时间新买台pc再把实践代码放出来吧）

## 相关
- 调试工具：ARC
chrome下的一款插件
![ARC](ARC.png)
又忍不住炫耀下蓝灯侠（lantern)真的好用，现在妈妈再也不用担心我不能直接在chrome应用商店下载插件了-。-
- 相关学习内容:[理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)

