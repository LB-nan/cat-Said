---
layout: post
title: burpsuite的使用
categories: 安全工具
description: burpsuite使用
keywords: burpsuite
---

burpsuite使用及配置

### 1、burp suite简介

打开burp suite的导航栏有如下内容：

1. burp
2. project：项目
3. intruder：测试器
4. repeater：重发器
5. window：窗口
6. help：帮助

第二行工具栏有如下按钮：

1. dashboard：仪表盘
2. target：目标
   1. sitemap：网站地图
   2. scope：范围
   3. issue definitions：问题定义
3. proxy：代理
   1. intercept：截断
   2. HTTP history：HTTP历史记录
   3. websockets history：websocket历史
   4. options：选项
4. intruder：测试器
   1. 发送到这里的每个请求都是一个tab
5. repeater：重发器
   1. 发送到这里的每个请求都是一个tab
6. sequencer：定序器
   1. live capture：实时捕捉
   2. Manual load：手动加载
   3. analysis options：分析选项
7. decoder：编码器
8. comparer：对比器
9.  extender：插件扩展
    1.  已有的扩展
    2.  商店
    3.  API
    4.  选项
10. project options：项目选项
11. user options：用户选项
12. 如安装其他扩展会在后面

### 2、浏览器设置代理

以火狐为例，安装一个插件`FoxyProxy`，然后设置代理端口为`127.0.0.1:8080`即可。

部分版本的burp suite抓不到HTTPS的包，需要以下流程

1. 打开burp suite
2. 浏览器打开输入`localhost:8080`
3. 点击右上角的`CA Certificate`，下载到电脑
4. 打开火狐的设置 -》 隐私与安全
5. 下拉找到证书 =》 查看证书 =》 导入，即可

### 3、抓包

火狐切到本地代理，burp suite在`proxy => options`里面新建一个监听`127.0.0.1:8080`，然后打开监听`proxy => Intercept is off =>  Intercept is on`,然后浏览器配置了代理之后随便打开个网页，就抓到了这个HTTP的包。

抓到之后旁边的一些按钮会亮起，它们的功能分别是： `Forward`的功能是当你查看过消息或者重新编辑过消息之后，点击此按钮，将发送消息至服务器端。 `Drop`的功能是你想丢失当前拦截的消息，不再forward到服务器端。 `Interception is on`表示拦截功能打开，拦截所有通过Burp Proxy的请求数据；`Interception is off`表示拦截功能关闭，不再拦截通过Burp Proxy的所有请求数据。 `Action`的功能是除了将当前请求的消息传递到Spider、Scanner、Repeater、Intruder、Sequencer、Decoder、Comparer组件外，还可以做一些请求消息的修改，如改变GET或者POST请求方式、改变请求body的编码，同时也可以改变请求消息的拦截设置，如不再拦截此主机的消息、不再拦截此IP地址的消息、不再拦截此种文件类型的消息、不再拦截此目录的消息，也可以指定针对此消息拦截它的服务器端返回消息；还有一个`Open Browser`打开浏览器

浏览器访问的一些页面也会存到`HTTP history`里面，可以把里面的一些记录添加到目标的范围里面。当设置了某个范围，其他的网址就不会被捕捉了。

### 4、监听

当我们在实际使用中，可能需要同时测试不同的应用程序时，我们可以通过设置不同的代理端口，来区分不同的应用程序，Proxy监听即提供这样的功能设置。`proxy => options =》 add`

Proxy监听设置主要包含3块功能：

1. 端口和IP绑定设置Binding 绑定的端口port是指Burp Proxy代理服务监听的端口，绑定IP地址分仅本地回路、所有接口、指定地址三种模式，在渗透测试中，无论你选择哪种模式，你需要明白一点，当你选择的非本地回路IP地址时，同局域网内的其他电脑也可以访问你的监听地址。
2. 请求处理Request Handling 请求处理主要是用来控制接受到Burp Proxy监听端口的请求后，如何对请求进行处理的。其具体配置可分为：端口的转发、主机名/域名的转发、强制使用SSL和隐形代理4个部分。当我们配置了端口的转发时，所有的请求都会被转发到这个端口上；如果我们配置了主机或域名的转发，则所有的请求会转发到指定的主机或域名上。同时，我们可以指定，通过Burp Proxy的消息是否强制使用SSL，如果设置了此项，则请求若是http协议，经Burp proxy代理后将转换为https协议。隐形代理主要是用于测试富客户端应用或者是非浏览器代理方式的应用，当我们设置了它,访问这些应用时，将通过非代理的方式，直接连接Burp Proxy的监听端口。
3. SSL 证书 这些设置控制呈现给SSL客户端的服务器SSL证书。可以解决使用拦截代理时出现的一些SSL问题： 
   1. 您可以消除您的浏览器的SSL警报，并需要建立SSL例外。其中，网页加载来自其他域的SSL保护的项目，可以确保这些正确的加载到浏览器，而不需要为每个域手动接受代理的SSL证书。 
   2. 2.可以与该拒绝无效的SSL证书连接到服务器胖客户机应用程序的工作。 

### 5、扫描

当抓住一个包之后，可以进行扫描`scan`，打开后会有一个弹窗

![burpsuiteScan](/images/blog/img/burpsuiteScan.png)

扫描完了就会放到仪表盘中，在右侧的窗口内会有一些有问题的数据出来，会标识是什么问题，可以点击每一项查看问题和扫描的请求和响应内容。可以在仪表盘打开详细内容，然后查看或保存。

### 6、Intruder 测试器

Burp Intruder作为Burp Suite中一款功能极其强大的自动化测试工具，通常被系统安全渗透测试人员被使用在各种任务测试的场景中。

它的工作原理是：Intruder在原始请求数据的基础上，通过修改各种请求参数，以获取不同的请求应答。每一次请求中，Intruder通常会携带一个或多个有效攻击载荷（Payload),在不同的位置进行攻击重放，通过应答数据的比对分析来获得需要的特征数据。Burp Intruder通常被使用在以下场景：

1. 标识符枚举 Web应用程序经常使用标识符来引用用户、账户、资产等数据信息。例如，用户名，文件ID和账户号码。
2. 提取有用的数据 在某些场景下，而不是简单地识别有效标识符，你需要通过简单标识符提取一些其他的数据。比如说，你想通过用户的个人空间id，获取所有用户在个人空间标准的昵称和年龄
3. 模糊测试 很多输入型的漏洞，如SQL注入，跨站点脚本和文件路径遍历可以通过请求参数提交各种测试字符串，并分析错误消息和其他异常情况，来对应用程序进行检测。由于的应用程序的大小和复杂性，手动执行这个测试是一个耗时且繁琐的过程。这样的场景，您可以设置Payload，通过Burp Intruder自动化地对Web应用程序进行模糊测试。

通常来说，使用Burp Intruder进行测试，主要遵循以下步骤：

1. 确认Burp Suite安装正确并正常启动，且完成了浏览器的代理设置。
2. 进入Burp Proxy选项卡，关闭代理拦截功能。
3. 进行历史日志（History）子选项卡，查找可能存在问题的请求日志，并通过右击菜单，发送到Intruder。
4. 进行Intruder 选项卡，打开Target和Positions子选项卡。这时，你会看到上一步发送过来的请求消息。
5. 因为我们了解到Burp Intruder攻击的基础是围绕刚刚发送过来的原始请求信息，在原始信息指定的位置上设置一定数量的攻击载荷Payload，通过Payload来发送请求获取应答消息。
6. 在Position界面的右边，有`Add $`、`Clear $`、`Auto $`、`Refersh $`四个按钮，是用来控制请求消息中的参数在发送过程中是否被Payload替换，如果不想被替换，则选择此参数，点击`Clear $`,即将参数前缀$去掉。
7. 当我们打开Payload 子选项卡，选择Payload的生成或者选择策略，默认情况下选择“Simple list",当然你也可以通过下拉选择其他Payload类型或者手工添加。
8. 此时，我们再回到Position界面，在界面的右上角，点击`Start attack`，发起攻击。
9. 此时，Burp会自动打开一个新的界面，包含攻击执行的情况、Http状态码、长度等结果信息。
10. 我们使用结果选项卡中的过滤器，对结果信息进行筛选。结果选项卡中所展示的列我们是可以进行指定的，我们可以在菜单Columns进行设置。
11. 选择我们需要的列，点击`Save`按钮，对攻击结果进行保存。保存之前我们也可以对保存的内容进行设置。

#### 6.1 Payload类型与处理

在Burp Intruder的Payload选项卡中，有Payload集合的设置选项，包含了经常使用的Payload类型，共18种。

常用的几种如下：

1. 简单列表（Simple list） ——最简单的Payload类型，通过配置一个字符串列表作为Payload，也可以手工添加字符串列表或从文件加载字符串列表。
2. 数字类型（Number）——这种类型的Payload是指根据配置，生成一系列的数字作为Payload。
3. 日期类型（Dates）——这种类型的Payload是指根据配置，生成一系列的日期。

#### 6.2 Intruder的攻击类型

在处理变量的上方，有一个下拉选择框，攻击类型（Attack Type）

1. 狙击手模式（Sniper）：它使用一组Payload集合，依次替换Payload位置上（一次攻击只能使用一个Payload位置）被§标志的文本（而没有被§标志的文本将不受影响），对服务器端进行请求，通常用于测试请求参数是否存在漏洞。
2. 攻城锤模式（Battering ram）：它使用单一的Payload集合，依次替换Payload位置上被§标志的文本（而没有被§标志的文本将不受影响），对服务器端进行请求，与狙击手模式的区别在于，如果有多个参数且都为Payload位置标志时，使用的Payload值是相同的，而狙击手模式只能使用一个Payload位置标志。
3. 草叉模式（Pitchfork ）：它可以使用多组Payload集合，在每一个不同的Payload标志位置上（最多20个），遍历所有的Payload。举例来说，如果有两个Payload标志位置，第一个Payload值为A和B，第二个Payload值为C和D，则发起攻击时，将共发起两次攻击，第一次使用的Payload分别为A和C，第二次使用的Payload分别为B和D。
4. 集束炸弹模式（Cluster bomb）：它可以使用多组Payload集合，在每一个不同的Payload标志位置上（最多20个），依次遍历所有的Payload。它与草叉模式的主要区别在于，执行的Payload数据Payload组的乘积。

#### 6.3 Intruder的可选项设置

可选项设置主要包括请求消息头设置、请求引擎设置、攻击结果设置、grep match, grepextract, grep payloads,以及重定向设置。在使用中，你可以在攻击前进行设置，也可以在攻击过程中做这些选项的调整。

1. 请求消息头设置（Request Headers）：这个设置主要用来控制请求消息的头部信息
2. 请求引擎设置（Request Engine）：这个设置主要用来控制Burp Intruder攻击，合理地使用这些参数能更加有效地完成攻击过程。它有如下参数
   1. Number of threads并发的线程数
   2. Number of retries on network failure 网络失败时候重试次数
   3. Pause beforeretry重试前的暂停时间间隔（毫秒）
   4. Throttle between requests 请求延时（毫秒）
   5. Start time开始时间，启动攻击之后多久才开始执行
3. Grep Match：这个设置主要用来从响应消息中提取结果项，如果匹配，则在攻击结果中添加的新列中标明，便于排序和数据提取。
4. Grep Extract：这些设置可用于提取响应消息中的有用信息。对于列表中配置的每个项目，Burp会增加包含提取该项目的文本的新结果列。然后，您可以排序此列（通过单击列标题）命令所提取的数据。
5. Grep Payloads：这些设置可用于提取响应消息中是否包含Payload的值
6. 重定向（Redirections）：这些设置主要是用来控制执行攻击时Burp如何处理重定向，在实际使用中往往是必须遵循重定向，才能实现你的攻击目的。

### 7、Burp Repeater

Burp Repeater作为Burp Suite中一款手工验证HTTP消息的测试工具，通常用于多次重放请求响应和手工修改请求消息的修改后对服务器端响应的消息分析。

在渗透测试过程中，我们经常使用Repeater来进行请求与响应的消息验证分析，比如修改请求参数，验证输入的漏洞；修改请求参数，验证逻辑越权；从拦截历史记录中，捕获特征性的请求消息进行请求重放。

在我们使用Burp Repeater时，通常会结合Burp的其他工具一起使用，比如Proxy的历史记录，Scanner的扫描记录、Target的站点地图等，通过其他工具上的右击菜单，执行`Sendto Repeater`，跳转到Repeater选项卡中，然后才是对请求消息的修改以及请求重放、数据分析与漏洞验证。

#### 7.1 可选项设置（Options）

与Burp其他工具的设置不同，Repeater的可选项设置菜单位于整个界面顶部的菜单栏中，其设置主要包括以下内容：

1. 更新Content-Length：这个选项是用于控制Burp是否自动更新请求消息头中的Content-Length
2. 解压和压缩（Unpack gzip / deflate ）：这个选项主要用于控制Burp是否自动解压或压缩服务器端响应的内容
3. 跳转控制（Follow redirections）：这个选项主要用于控制Burp是否自动跟随服务器端作请求跳转，比如服务端返回状态码为302，是否跟着应答跳转到302指向的url地址。它有4个选项，分别是永不跳转（Never），站内跳转（On-site only ）、目标域内跳转（In-scope only）、始终跳转（Always），其中永不跳转、始终跳转比较好理解，站内跳转是指当前的同一站点内跳转；目标域跳转是指target scope中配置的域可以跳转；
4. 跳转中处理Cookie（Process cookies in redirections ）：这个选项如果选中，则在跳转过程中设置的Cookie信息，将会被带到跳转指向的URL页面，可以进行提交。
5. 其他操作（Action）：通过子菜单方式，指向Burp的其他工具组件中。

### 8、Burp Sequencer

Burp Sequencer作为Burp Suite中一款用于检测数据样本随机性质量的工具，通常用于检测访问令牌是否可预测、密码重置令牌是否可预测等场景，通过Sequencer的数据样本分析，能很好地降低这些关键数据被伪造的风险。

Burp Sequencer作为一款随机数分析的工具，在分析过程中，可能会对系统造成不可预测的影响，在你不是非常熟悉系统的情况下，建议不要在生产环境进行数据分析。它的使用步骤大体如下：

1. 首先，确认Burp Suite安装正确，并配置好浏览器代理，正常运行。
2. 从BurpProxy的历史日志记录中，寻找token或类似的参数，返回右击弹出上下文菜单，点击`Sendto Sequencer`。
3. 进入Burp Sequencer的Live Capture面板，选中刚才发送过来的记录，点击`Configure`配置需要分析的token或者参数。
4. 在弹出的参数配置对话框中，选中参数的值，点击`OK`按钮，完成参数设置。
5. 点击`Select Live Capture`，开始进行参数值的获取。
6. 当抓取的参数值总数大于100时，点击`pause`或者`stop`，这时可以进行数据分析，点击`Analyze now`即进行数据的随机性分析。
7. 等分析结束，则可以看到分析结果的各种图表。
8. 当然，我们也可以把获取的数据保存起来，下一次使用的时候，从文件加载参数，进行数据分析。
9. 当我再次使用时，直接加载数据进行分析即可。

#### 8.1 可选项设置（Analysis Options）

分析可选项设置的目的主要是为了控制token或者参数，在进行数据分析过程中，需要做什么样的处理，以及做什么类型的随机性分析。它主要由令牌处理（Token Handling）和令牌分析（Token Analysis）两部分构成。

1. 令牌处理Token Handling主要控制令牌在数据分析中如何被处理，Pad short tokens at start / end 表示如果应用程序产生的令牌是具有可变长度的； Padwith 表示你可以指定将用于填充的字符。Base64-decode before analyzing表示在数据分析是否进行base64解码
2. 令牌分析Token Analysis主要用来控制对数据进行随机性分析的类型，我们可以选择多个分析类型，也可以单独启用或禁用每个字符类型级和字节级测试。

### 9、Burp Decoder

Burp Decoder的功能比较简单，作为Burp Suite中一款编码解码工具，它能对原始数据进行各种编码格式和散列的转换。主要由输入域、输出域、编码解码选项三大部分组成。

输入域即输入需要解码的原始数据，此处可以直接填写或粘贴，也可以通过其他Burp工具的上下文菜单中`Send to Decoder`；输出域即对输入域进行解码的结果显示出来。无论是输入域还是输出域都支持文本与Hex两种格式，其中编码解码选项中，由解码选项（Decodeas)、编码选项（Encode as)、散列（Hash）三个构成。实际使用中，可以根据场景的需要进行设置。对于编码解码选项，目前支持URL、HTML、Base64、ASCII、16进制、8进制、2进制、GZIP共八种形式的格式转换，Hash散列支持SHA、SHA-224、SHA-256、SHA-384、SHA-512、MD2、MD5格式的转换，更重要的是，对于同一个数据，我们可以在Decoder的界面，进行多次编码解码的转换。

### 10、Burp Comparer

Burp Comparer在Burp Suite中主要提供一个可视化的差异比对功能，来对比分析两次数据之间的区别。使用中的场景可能是：

1. 枚举用户名过程中，对比分析登陆成功和失败时，服务器端反馈结果的区别。
2. 使用 Intruder 进行攻击时，对于不同的服务器端响应，可以很快的分析出两次响应的区别在哪里。
3. 进行SQL注入的盲注测试时，比较两次响应消息的差异，判断响应结果与注入条件的关联关系。

对于Comparer的使用，主要有两个环节组成，先是数据加载，然后是差异分析。 Comparer数据加载的方式常用的有：从其他Burp工具通过上下文菜单转发过来、直接粘贴、从文件加载三种方式。当加载完毕后，如果你选择了两次不同的请求或应答消息，则下发的比较按钮将被激活，可以选择文本比较或者字节比较。

如果点击了`words`或者`bytes`，则进入比对界面，页面自动通过背景颜色显示数据的差异。

### 11、project options

在Burp Suite中，存在一些粗粒度的设置，这些设置选项，一旦设置了将会对Burp Suite的整体产生效果，这就是Burp Suite中Options面板。当我们打开Options面板即可看到，它是由Connections、HTTP、TLS、Sessions、Misc五个选项卡组成(版本不同不一致)。

#### 11.1 网络连接设置/Connections选项卡

Connections选项卡主要用来控制Burp如何来处理平台认证、上游代理服务器、Socks代理、超时设置、主机名或域名解析以及Scope之外的请求六个方面的相关配置。当我们打开Connections选项卡，从上往下拖动，首先看到的设置将是平台身份认证（PlatformAuthentication）。

- 平台身份认证（Platform Authentication）：设置允许你配置Burp自动执行到目标Web服务器的平台身份验证，不同的主机可以配置不同的认证方式和证书。

然后是`Upstream Proxy Servers`

- 上游代理服务器（Upstream Proxy Servers）
- 设置主要是控制Burp是否会发送请求到上游代理服务器，或直转向目标Web服务器。
- 我们可以配置多个匹配规则当我们配置了多个规则时，可以针对不同的目标主机或主机组指定不同的代理服务器设置。这些规则将按照顺序，并将与目标Web服务器相匹配的第一个规则作为生效规则。如果列表没有规则匹配，Burp默认采取直连、非代理的方式进行连接。

然后是`Socks代理`

- 设置允许我们配置Burp使用SOCKS代理的方式进行所有传出的通信，但此设置只在TCP层生效，所有出站请求将通过这个代理发送。如果我们同时设置了已游HTTP代理服务器配置的规则，则请求上游代理将通过这里配置的SOCKS代理发送。其请求的匹配路径依次是：本地-->上游代理-->SOCKS代理。 在使用SOCKS代理时，我们需要勾选`Use SOCKS proxy`，并提供代理的ip或者主机名、端口、认证的用户名和口令。如果我们勾选了`Do DNS lookups over SOCKS proxy`，则进行域名解析时，将通过SOCKS代理去查询，而不会使用本地缓存。

然后是`Timeouts`

- 超时设置（Timeouts）
- 设置主要用于指定Burp各种网络任务的超时。

我们可以对以下超时项进行设置：

1. 正常（Normal ） - 此设置用于大多数网络通信，并确定Burp怎样放弃请求和记录已发生超时前等待。
2. 开放式应答（Open-ended responses） - 该设置只用在一个响应正在处理不包含内容长度或传输编码HTTP标头。在这种情况下，Burp确定传输已经完成之前等待指定的时间间隔。
3. 域名解析（Domain name resolution ） - 此设置确定Burp如何重新进行成功的域名查找，如果目标主机地址频繁变化时需要设定为一个适当的低的值。
4. 失败的域名解析（Failed domain name resolution ） - 此设置确定Burp多久会重新尝试不成功的域名查找。

然后是`Hostname Resolution`

- 此项配置比较简单，通过这些设置，我们可以指定主机名映射到IP地址，来覆盖本地计算机提供的DNS解析。每个主机名解析规则需要指定主机名，并与主机名相关联的IP地址。同时，每一个规则可以单独启用或禁用来控制其是否生效。

最后是`Out-of-Scope Requests`

- 可用于防止Burp发送任何超出Target面板中设置的Scope范围之外的请求，当我们需要保证没有请求到不在Scope范围内为它是有用的。

#### 11.2 Session设置

会话处理规则（Session Handling Rules）

Burp允许你自定义会话处理规则的列表，这能让我们细粒度地控制Burp如何处理应用程序的会话处理机制和相关功能。
