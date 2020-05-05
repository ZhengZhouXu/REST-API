# 如何设计 REST API

学习 REST 是一回事，而将所学的概念应用于真实的项目时，这完全是另一种挑战。在此教程中，我们将学习如何为一个网络应用设计 REST API。需要注意的是，在整个练习中，你将学习如何将 REST 原则应用于设计中。

**设计 REST 服务的步骤**  
[Identify Object Model ](#)
[Create Model URIs ](#)
[Determine Representations ](#)
[Assign HTTP Methods ](#)
[More Actions ](#)

#### Identify Object Model

设计基于 REST API 的应用程序的第一步是 —— 识别将被作为资源的对象。

对于一个网络应用程序来说，对象建模是相对简单的。可能有很多像: devices, managed entities, routers, modems etc 的东西。但简单起见，我们只考虑两个资源:

- Devices

- Configurations

Configurations 将作为 Devices 的一个子资源，一个 Devices 将包涵多个 Configurations 选项。  
请注意上述模型中的 objects/resources 都有一个整数 id 的作为唯一标识符。

#### Create Model URIs

当对象模型准备就绪时，那么现在就要决定资源 URIs。在设计资源 URIs 这一步时 —— 应关注资源与其子资源之间的关系。这些资源 URIs 将作为 RESTful 服务的端点。

在我们的应用程序中，Device 将作为顶级资源，而 Configurations 将作为 Device 的子资源。让我们写如下的 URLs:

```http
/devices
/devices/{id}

/configurations
/configurations/{id}

/devices/{id}/configurations
/devices/{id}/configurations/{id}
```

注意在这些 URIs 中，没有使用任何动词或操作的词语。在 URIs 中不包含动词是非常重要的。URIs 应该只由名词组成。

#### Determine Representations

当我们确定了资源 URLs 后，让我们来确定它们的表达示（representations）。多数表达示使用 XML 或 JSON 格式。我们将使用 XML 的示例，它更能表现数据是如何组成的。

#### Collection of Device Resource

在返回数据的集合时，应当只返回资源的重要信息。这将保证数据足够小，并且提高 REST APIs 的性能。

```xml
<devices size="2">

    <link rel="self" href="/devices"/>

    <device id="12345">
        <link rel="self" href="/devices/12345"/>
        <deviceFamily>apple-es</deviceFamily>
        <OSVersion>10.3R2.11</OSVersion>
        <platform>SRX100B</platform>
        <serialNumber>32423457</serialNumber>
        <connectionStatus>up</connectionStatus>
        <ipAddr>192.168.21.9</ipAddr>
        <name>apple-srx_200</name>
        <status>active</status>
    </device>

    <device id="556677">
        <link rel="self" href="/devices/556677"/>
        <deviceFamily>apple-es</deviceFamily>
        <OSVersion>10.3R2.11</OSVersion>
        <platform>SRX100B</platform>
        <serialNumber>6453534</serialNumber>
        <connectionStatus>up</connectionStatus>
        <ipAddr>192.168.20.23</ipAddr>
        <name>apple-srx_200</name>
        <status>active</status>
    </device>

</devices>
```

#### Single Device Resource

与 URI 集合相反，单个 Device 的 URI 中应包含资源完整的信息。此外还包括了所有子资源和可执行操作的链接列表。这将使你的 REST API [HATEOAS](#)驱动。

```xml
<device id="12345">
    <link rel="self" href="/devices/12345"/>

    <id>12345</id>
    <deviceFamily>apple-es</deviceFamily>
    <OSVersion>10.0R2.10</OSVersion>
    <platform>SRX100-LM</platform>
    <serialNumber>32423457</serialNumber>
    <name>apple-srx_100_lehar</name>
    <hostName>apple-srx_100_lehar</hostName>
    <ipAddr>192.168.21.9</ipAddr>
    <status>active</status>

    <configurations size="2">
        <link rel="self" href="/configurations" />

        <configuration id="42342">
            <link rel="self" href="/configurations/42342" />
        </configuration>

        <configuration id="675675">
            <link rel="self" href="/configurations/675675" />
        </configuration>
    </configurations>

    <method href="/devices/12345/exec-rpc" rel="rpc"/>
    <method href="/devices/12345/synch-config"rel="synch device configuration"/>
</device>
```

#### Configuration Resource Collection

与设备的表示类似，创建 configuration 集合表示时，也应使用最少的信息。

```xml
<configurations size="20">
  <link rel="self" href="/configurations" />
  <configuration id="42342">
    <link rel="self" href="/configurations/42342" />
  </configuration>

  <configuration id="675675">
    <link rel="self" href="/configurations/675675" />
  </configuration>
  ...
  ...
</configurations>
```

请注意在 device 中的 configurations 集合表示与顶级(top-level)的 configurations URI 是类似的。唯一的区别是 device 中的 configurations 只有两条，因此在设备中仅有 2 条 configurations 作为子资源被列出。

#### Single Configuration Resource

单个 configuration 资源表示必须包含该资源所有可能的信息 —— 包含相关链接  
。

```xml
<configuration id="42342">
    <link rel="self" href="/configurations/42342" />
    <content><![CDATA[...]]></content>
    <status>active</status>
    <link  rel="raw configuration content" href="/configurations/42342/raw" />
</configuration>
```

#### Configuration Resource Collection Under Single Device

Configuration 资源集合将作为 Configuration 主集合的子集合，并且依附于特定的 device 中。由于它是主集合的子集，不要创建主集合外的表示字段。使用与主集合相同的表示。

```xml
<configurations size="2">
    <link rel="self" href="/devices/12345/configurations" />

    <configuration id="53324">
        <link rel="self" href="/devices/12345/configurations/53324" />
        <link rel="detail" href="/configurations/53324" />
    </configuration>

    <configuration id="333443">
        <link rel="self" href="/devices/12345/configurations/333443" />
        <link rel="detail" href="/configurations/333443" />
    </configuration>
</configurations>
```

请注意，此子资源集合有两个链接。一个是作为子集合中的直接表示，即:

```http
/devices/12345/configurations/333443
```

另一个则指向它在主集合中的位置，即:

```http
/configurations/333443
```

拥有两个链接是非常重要的，如此你就能够用特定的方式去访问特定 device 下 configuration，你将有能力去隐藏一些字段(如果设计需要的话)，这些字段将不在次集合中显示。

#### Single Configuration Resource Under Single Device

该表示应该与主集合中的 configuration 表示相似，或者隐藏少许字段。

该子资源表示也应该有一个指向主表示的链接。

```xml
<configuration id="11223344">
    <link rel="self" href="/devices/12345/configurations/11223344" />
    <link rel="detail" href="/configurations/11223344" />
    <content><![CDATA[...]]></content>
    <status>active</status>
    <link rel="raw configuration content" href="/configurations/11223344/raw" />
</configuration>
```

现在，在进入下一节之前，让我们记录一些你可能容易忽视的地方。

- 资源 URIs 都是名词

- URIs 通常有两种形式 - 集合资源和单个资源集合可能有两种形式，主集合和次集合，次集合仅仅是来自主集合的子集合。

- 集合应该有主集合和次集合两种形势，次集合将作为主集合的子资源存在。

- 每个资源/集合包含一个指向自身的链接。

- 集合仅包含对资源最重要的信息。

- 获取完整的资源信息，需要通过特定的资源 URI 获取。

- 表示能够拥有额外的链接(即在一个链接中的方法组)具有代表性的方法，如 POST 方法。你能通过全新的方式，拥有更多属性和形式的链接。

- 我们还尚未谈论在这些资源上的操作。

## Assign HTTP Methods

就此我们已经固定资源 URIs 和表示。现在让我们来确定应用程序上其他可能的操作，并将这些操作映射到资源 URIs 上。一个网络应用程序的用户，应当可以进行浏览，创建，更新，删除操作。让我们来映射这些操作。

#### Browse all devices or configurations [Primary Collection]

```http
HTTP GET /devices
HTTP GET /configurations
```

如果集合数量很大，你可以使用分页和过滤。例如，以下请求将获取集合的前 20 条记录。

```http
HTTP GET /devices?startIndex=0&size=20
HTTP GET /configurations?startIndex=0&size=20
```

#### Browse all devices or configurations [Secondary Collection]

```http
HTTP GET /devices/{id}/configurations
```

这通常是一个较小的集合 —— 所以无需过滤或截断。

#### Browse single device or configuration [Primary Collection]

对 device 或 configuration 获取完整的详情时，在单个资源 URIs 上使用 GET 操作。

```http
HTTP GET /devices/{id}
HTTP GET /configurations/{id}
```

#### Browse single device or configuration [Secondary Collection]

```http
HTTP GET /devices/{id}/configurations/{id}
```

子资源表示将与主表示的子集相似。

#### Create a device or configuratio

创建是一个非幂等操作，在 HTTP 协议中 —— POST 也是非幂等操作，所以使用 POST 请求。

```http
HTTP POST /devices
HTTP POST /configurations
```

请注意，在该请求的负载中没有包含任何 id 属性，因为 id 是由服务端负责产生。

创建操作的响应类似于下面这样：

```http
HTTP/1.1 201 Created
Content-Type: application/xml
Location: http://example.com/network-app/configurations/678678

<configuration id="678678">
    <link rel="self" href="/configurations/678678" />
    <content><![CDATA[...]]></content>
    <status>active</status>
    <link  rel="raw configuration content" href="/configurations/678678/raw" />
</configuration>
```

#### Update a device or configuration

更新操作是一个幂等操作，在 HTTP 中 PUT 也是一个幂等方法，因此我们可以使用 PUT 方法来执行更新操作。

```http
HTTP PUT /devices/{id}
HTTP PUT /configurations/{id}
```

PUT 的响应类似如下：

```http
HTTP/1.1 200 OK
Content-Type: application/xml

<configuration id="678678">
    <link rel="self" href="/configurations/678678" />
    <content><![CDATA[. updated content here .]]></content>
    <status>active</status>
    <link  rel="raw configuration content" href="/configurations/678678/raw" />
</configuration>
```

#### Remove a device or configuration

删除应该使用 DELETE 请求。

```http
HTTP DELETE /devices/{id}
HTTP DELETE /configurations/{id}
```

如果资源已在队列中等待被删除（异步操作），那么应该返回 202（已接受）。如果资源已被永久删除（同步操作），则应返回 200（确认）或 204（无内容）。

在异步操作中，应用程序应该返回一个任务 id，以便跟踪成功或失败状态。

请注意在删除系统资源时，应该有充足的分析，并且足够肯定。通常来说，你会希望在一个请求中软删除一个资源 —— 即将这些资源状态设置为不活跃。通过这种方式，你也不需要再其他地方查找和删除资源的引用。

#### Applying or Removing a configuration from a device

在真实的应用程序中，你可能需要将 configuration 应用到对应的 device 中 —— 获取你可能需要从对应的 device 中删除 configuration（不是来自主集合）。由于其幂等性，你应当使用 PUT 和 DELETE 方法。

```http
// Apply Configuration on a device
HTTP PUT /devices/{id}/configurations

// Remove Configuration on a device
HTTP DELETE /devices/{id}/configurations/{id}
```

## More Actions

目前为止，我们仅设计了对象模型，URIs 和决定对其操作的 HTTP 方法。你还需要处理应用程序的其他方面：

1. Logging

2. [Security](https://restfulapi.net/security-essentials/)

3. Discovery etc.

在接下来的文章中，我们将用 java 创建该应用程序，以获得更详尽的理解。
