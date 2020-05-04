# 使用JAX-RS 2.0创建REST APIs

在REST API设计教程中，我们学习了如何在一个网络应用程序中使用REST原则。在这篇文章，我们将学习如何使用[JAX-RS 2.0](https://github.com/jax-rs)创建REST APIs（RESTful服务的Java API）。

> Table of Contents
> 
> [JAX-RS 2.0 Specification](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#jax-rs-20)
> [JAX-RS 2.0 Annotations](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#jax-rs-annotations)
> [Create Maven Application](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#create-application)
> [Include JAX-RS Dependencies to Application](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#jax-rs-dependencies)
> [Create Resource Representations](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#resource-representations)
> [Create REST Resource](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#create-rest-resource)
> [Register Resource in runtime](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#register-resource)
> [Demo](https://restfulapi.net/create-rest-apis-with-jax-rs-2-0/#demo)

## JAX-RS 2.0 Specification

> JAX-RS为开发基于REST风格的网络应用程序提供了便捷的APIs

Java EE 6通过对RESTful网络服务的Java API实现（JAX-RS）[[JSR 311](https://javaee.github.io/jsr311/)]，迈向了标准化RESTful网站服务的第一步。JAX-RS确保REST API代码在所有Java EE-compliant应用程序服务的可移植性。最新版本的JAX-RS 2.0[[JSR 339](https://jcp.org/en/jsr/detail?id=339)]，已作为Java EE 7平台的一部分发布。

JAX-RS专注于将Java批注（annotations）应用于纯Java对象。JAX-RS将批注和HTTP操作绑定到你Java类中的单个方法。它还有帮助你处理输入/输出的批注。

当我们准备讲讲JAX-RS规范时，也就意味着我们需要实现一个可运行的REST API代码。当下比较流行的JAX-RS实现有：

- [Jersey](https://jersey.github.io/)

- [RESTEasy](https://resteasy.github.io/)

- [Apache CXF](https://cxf.apache.org/)

- [Restlet](https://restlet.com/)

## JAX-RS 2.0 Annotations

让我们看一下JAX-RS 2.0提供的一些重要注释。

<div>
    <table >
        <tr>
            <th>ANNOTATION</th>
            <th>DESCRIPTION</th>
        </tr>
        <tr>
            <td>@Path(‘resourcePath’)</td>
            <td>该批注用于匹配基础路径，能在类上或方法上指定。
                <pre>
 @Path("/configurations")
 public class ConfigurationResource 
 {
      @Path("/{id}")
      @GET
      public Response getConfigurationById(@PathParam("id") Integer id){  
          ...
      }
  }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@POST</td>
            <td>
                <span>批注方法将处理所匹配资源路径的HTTP POST请求</span>
                <pre>
 @POST
 @Consumes("application/xml")
 public Response createConfiguration(Configuration config) {
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@PUT</td>
            <td>
                <span>批注方法将处理所匹配资源路径的HTTP PUT请求</span>
                <pre>
 @PUT
 @Consumes("application/xml")
 public Response updateConfiguration(@PathParam("id") Integer id, Configuration config){
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@GET</td>
            <td>
                <span>批注方法将处理所匹配资源路径的HTTP GET请求</span>
                <pre>
 @GET
 @Path("/{id}")
 public Response getConfigurationById(@PathParam("id") Integer id){
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@DELETE</td>
            <td>
                <span>批注方法将处理所匹配资源路径的HTTP GET请求</span>
                <pre>
 @DELETE
 @Path("/{id}")
 public Response deleteConfiguration(@PathParam("id") Integer id){
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@PathParam("parameterName")</td>
            <td>
                <span>该批注用于将来自URL中的值（资源标识符）注入到方法参数中</span>
                <pre>
 @DELETE
 @Path("/{id}")
 public Response deleteConfiguration(@PathParam("id") Integer id){
     ...
 }
                </pre>
                <span>
                    在上述的事例中，来自/{id}的id值将匹配@PathParam("id") Integer id。
                    举个例子，URI HTTP DELETE /configurations/22312 将被映射到上述方法，
                    并且id将被填充为22312。
                </span>
            </td>
        </tr>
        <tr>
            <td>@Produces</td>
            <td>
                <span>
                    它定义了所批注的方法使用哪种MIME类型传递。它能在类级别（class level）中定义
                    也可以在方法级别（method level）中定义。
                    如果定义为类级别，在没有方法覆盖的情况下，该资源类中的所有方法都将
                    返回相同的MIME类型。
                </span>
                <pre>
 @Path("/configurations")
 @Produces("application/xml")
 public class ConfigurationResource {
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@Consumes</td>
            <td>
                <span>它定义批注资源将使用哪种MIME类型。</span>
                <pre>
 @POST
 @Consumes("application/xml")
 public Response createConfiguration(Configuration config) {
     ...
 }
                </pre>
            </td>
        </tr>
        <tr>
            <td>@Context</td>
            <td>
                <span>创建HATEOAS链接，JAX-RS 2.0提供了可以使用@Context批注获取的UriInfo</span>
                <pre>
 @Context
 UriInfo uriInfo;
                </pre>
            </td>
        </tr>
    </table>
</div>

> 默认情况下，如果没有明确的实现，JAX-RS运行时将自动支持HEAD和OPTIONS方法。对于HEAD方法，运行时将调用已实现的GET方法（如果存在），并且忽略响应实体（如果设置）。OPTIONS方法可以在Allow header中使用一组受支持的资源方法返回响应。

## Create Maven Application

[Maven](https://maven.apache.org/)是一个软件项目管理和综合（comprehension）的工具，包括项目构建、报告和文档，这些都来自于一个中央信息库pom.xml。

按以下步骤，在[eclipse](https://www.eclipse.org/)中使用maven创建一个应用程序：

* ##### Open new project wizard from **File > New > Maven Project**

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/Create-Maven-Application-Step-1.png)

* ##### Click on Next

![]()

* ##### Select maven-archtype-webapp

![]()

* ##### Fill project details and click on Finish

![]()

## Include JAX-RS Dependencies to Application

 [JDK 1.7](https://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html)已经捆绑了JAX-RS 2.0，因此在[JAVA_HOME](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/)中已经有JDK 1.7或高于此的版本，那你不需要单独引入JAX-RS。但是你至少应该包含上述列表中的一个。

在这个例子中，我使用[RESTEasy 3.1.2.Final](https://docs.jboss.org/resteasy/docs/3.1.2.Final/userguide/html_single/index.html)。

```java
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.restfulapi.app</groupId>
    <artifactId>NetworkManagement</artifactId>
    <packaging>war</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>NetworkManagement</name>
    <url>http://maven.apache.org</url>
    <repositories>
        <repository>
            <id>jboss</id>
            <name>jboss repo</name>
            <url>http://repository.jboss.org/nexus/content/groups/public/</url>
        </repository>
    </repositories>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
        <finalName>NetworkManagement</finalName>
    </build>
    <dependencies>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxrs</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxb-provider</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-servlet-initializer</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
         
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project><project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.restfulapi.app</groupId>
    <artifactId>NetworkManagement</artifactId>
    <packaging>war</packaging>
    <version>0.0.1-SNAPSHOT</version>
    <name>NetworkManagement</name>
    <url>http://maven.apache.org</url>
    <repositories>
        <repository>
            <id>jboss</id>
            <name>jboss repo</name>
            <url>http://repository.jboss.org/nexus/content/groups/public/</url>
        </repository>
    </repositories>
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.2</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
        <finalName>NetworkManagement</finalName>
    </build>
    <dependencies>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxrs</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-jaxb-provider</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
        <dependency>
            <groupId>org.jboss.resteasy</groupId>
            <artifactId>resteasy-servlet-initializer</artifactId>
            <version>3.1.2.Final</version>
        </dependency>
         
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            <scope>test</scope>
        </dependency>
    </dependencies>
</project>
```

> resteasy-servlet-initializer 工具可以自动扫描Servlet 3.0容器中的资源和提供程序。

## Create Resource Representations

在 JAX-RS中，资源的表示是用 [JAXB](https://www.oracle.com/technical-resources/articles/javase/jaxb.html)注释的POJO类，即`@XmlRootElement`, `@XmlAttribute` 和 `@XmlElement`等等

##### 1) Configurations collection resource

```java
package net.restfulapi.app.rest.domain;
 
import java.util.List;
 
import javax.ws.rs.core.Link;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.adapters.XmlJavaTypeAdapter;
 
@XmlRootElement(name = "configurations")
@XmlAccessorType(XmlAccessType.FIELD)
public class Configurations 
{
    @XmlAttribute
    private Integer size;
     
    @XmlJavaTypeAdapter(Link.JaxbAdapter.class)
    @XmlElement
    private Link link;
     
    @XmlElement
    private List<Configuration> configurations;
 
    public Integer getSize() {
        return size;
    }
 
    public void setSize(Integer size) {
        this.size = size;
    }
 
    public Link getLink() {
        return link;
    }
 
    public void setLink(Link link) {
        this.link = link;
    }
 
    public List<Configuration> getConfigurations() {
        return configurations;
    }
 
    public void setConfigurations(List<Configuration> configurations) {
        this.configurations = configurations;
    }
}
```

##### 2) Configuration resource

```java
package net.restfulapi.app.rest.domain;
 
import javax.ws.rs.core.Link;
import javax.xml.bind.annotation.XmlAccessType;
import javax.xml.bind.annotation.XmlAccessorType;
import javax.xml.bind.annotation.XmlAttribute;
import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;
import javax.xml.bind.annotation.adapters.XmlJavaTypeAdapter;
 
import net.restfulapi.app.rest.domain.common.Status;
 
@XmlRootElement(name="configuration")
@XmlAccessorType(XmlAccessType.FIELD)
public class Configuration 
{
    @XmlAttribute
    private Integer id;
    @XmlJavaTypeAdapter(Link.JaxbAdapter.class)
    @XmlElement
    private Link link;
    @XmlElement
    private String content;
    @XmlElement
    private Status status;
 
    public Link getLink() {
        return link;
    }
 
    public void setLink(Link link) {
        this.link = link;
    }
 
    public Integer getId() {
        return id;
    }
 
    public void setId(Integer id) {
        this.id = id;
    }
 
    public String getContent() {
        return content;
    }
 
    public void setContent(String content) {
        this.content = content;
    }
 
    public Status getStatus() {
        return status;
    }
 
    public void setStatus(Status status) {
        this.status = status;
    }
}
```

##### 3) Message resource [to inform client when no resource representation needed]

```java
package net.restfulapi.app.rest.domain.common;
 
import javax.xml.bind.annotation.XmlRootElement;
 
@XmlRootElement(name = "message")
public class Message {
     
    public Message() {
        super();
    }
 
    public Message(String content) {
        super();
        this.content = content;
    }
 
    private String content;
 
    public String getContent() {
        return content;
    }
 
    public void setContent(String content) {
        this.content = content;
    }
}
```

此外，我们还用ConfigurationDB类模拟数据库功能。并且开放了对configuration集合和单个configuration的增删改查（CURD）操作。

```java
package net.restfulapi.app.dao;
 
import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicInteger;
 
import net.restfulapi.app.rest.domain.Configuration;
import net.restfulapi.app.rest.domain.common.Status;
 
public class ConfigurationDB {
    private static Map<Integer, Configuration> configurationDB = new ConcurrentHashMap<Integer, Configuration>();
    private static AtomicInteger idCounter = new AtomicInteger();
     
    public static Integer createConfiguration(String content, Status status){
        Configuration c = new Configuration();
        c.setId(idCounter.incrementAndGet());
        c.setContent(content);
        c.setStatus(status);
        configurationDB.put(c.getId(), c);
         
        return c.getId();
    }
     
    public static Configuration getConfiguration(Integer id){
        return configurationDB.get(id);
    }
     
    public static List<Configuration> getAllConfigurations(){
        return new ArrayList<Configuration>(configurationDB.values());
    }
     
    public static Configuration removeConfiguration(Integer id){
        return configurationDB.remove(id);
    }
     
    public static Configuration updateConfiguration(Integer id, Configuration c){
        return configurationDB.put(id, c);
    }
}
```

## Create REST Resource

我们已在第二节中学习了JAX-RS的批注。让我们应用这些REST资源，并且将HTTP方法应用到REST资源上。

我已在代码上添加了详细的注释，对每个方法进行解释。

```java
package net.restfulapi.app.rest.service;
 
import java.util.List;
 
import javax.ws.rs.Consumes;
import javax.ws.rs.DELETE;
import javax.ws.rs.GET;
import javax.ws.rs.POST;
import javax.ws.rs.PUT;
import javax.ws.rs.Path;
import javax.ws.rs.PathParam;
import javax.ws.rs.Produces;
import javax.ws.rs.core.Context;
import javax.ws.rs.core.Link;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.UriBuilder;
import javax.ws.rs.core.UriInfo;
 
import net.restfulapi.app.dao.ConfigurationDB;
import net.restfulapi.app.rest.domain.Configuration;
import net.restfulapi.app.rest.domain.Configurations;
import net.restfulapi.app.rest.domain.common.Message;
import net.restfulapi.app.rest.domain.common.Status;
 
/**
 * This REST resource has common path "/configurations" and 
 * represents configurations collection resource as well as individual collection resources.
 * 
 * Default MIME type for this resource is "application/XML"
 * */
@Path("/configurations")
@Produces("application/xml")
public class ConfigurationResource 
{
    /**
     * Use uriInfo to get current context path and to build HATEOAS links 
     * */
    @Context
    UriInfo uriInfo;
     
    /**
     * Get configurations collection resource mapped at path "HTTP GET /configurations"
     * */
    @GET
    public Configurations getConfigurations() {
          
        List<Configuration> list = ConfigurationDB.getAllConfigurations();
          
        Configurations configurations = new Configurations();
        configurations.setConfigurations(list);
        configurations.setSize(list.size());
          
        //Set link for primary collection
        Link link = Link.fromUri(uriInfo.getPath()).rel("uri").build();
        configurations.setLink(link);
          
        //Set links in configuration items
        for(Configuration c: list){
            Link lnk = Link.fromUri(uriInfo.getPath() + "/" + c.getId()).rel("self").build();
            c.setLink(lnk);
        }
        return configurations;
    }
      
    /**
     * Get individual configuration resource mapped at path "HTTP GET /configurations/{id}"
     * */
    @GET
    @Path("/{id}")
    public Response getConfigurationById(@PathParam("id") Integer id){
        Configuration config = ConfigurationDB.getConfiguration(id);
         
        if(config == null) {
            return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND).build();
        }
          
        if(config != null){
            UriBuilder builder = UriBuilder.fromResource(ConfigurationResource.class)
                                            .path(ConfigurationResource.class, "getConfigurationById");
            Link link = Link.fromUri(builder.build(id)).rel("self").build();
            config.setLink(link);
        }
          
        return Response.status(javax.ws.rs.core.Response.Status.OK).entity(config).build();
    }
     
    /**
     * Create NEW configuration resource in configurations collection resource
     * */
    @POST
    @Consumes("application/xml")
    public Response createConfiguration(Configuration config){
        if(config.getContent() == null)  {
            return Response.status(javax.ws.rs.core.Response.Status.BAD_REQUEST)
                            .entity(new Message("Config content not found"))
                            .build();
        }
 
        Integer id = ConfigurationDB.createConfiguration(config.getContent(), config.getStatus());
        Link lnk = Link.fromUri(uriInfo.getPath() + "/" + id).rel("self").build();
        return Response.status(javax.ws.rs.core.Response.Status.CREATED).location(lnk.getUri()).build();
    }
     
    /**
     * Modify EXISTING configuration resource by it's "id" at path "/configurations/{id}"
     * */
    @PUT
    @Path("/{id}")
    @Consumes("application/xml")
    public Response updateConfiguration(@PathParam("id") Integer id, Configuration config){
         
        Configuration origConfig = ConfigurationDB.getConfiguration(id);
        if(origConfig == null) {
            return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND).build();
        }
         
        if(config.getContent() == null)  {
            return Response.status(javax.ws.rs.core.Response.Status.BAD_REQUEST)
                            .entity(new Message("Config content not found"))
                            .build();
        }
 
        ConfigurationDB.updateConfiguration(id, config);
        return Response.status(javax.ws.rs.core.Response.Status.OK).entity(new Message("Config Updated Successfully")).build();
    }
     
    /**
     * Delete configuration resource by it's "id" at path "/configurations/{id}"
     * */
    @DELETE
    @Path("/{id}")
    public Response deleteConfiguration(@PathParam("id") Integer id){
         
        Configuration origConfig = ConfigurationDB.getConfiguration(id);
        if(origConfig == null) {
            return Response.status(javax.ws.rs.core.Response.Status.NOT_FOUND).build();
        }
         
        ConfigurationDB.removeConfiguration(id);
        return Response.status(javax.ws.rs.core.Response.Status.OK).build();
    }
      
    /**
     * Initialize the application with these two default configurations
     * */
    static {
        ConfigurationDB.createConfiguration("Some Content", Status.ACTIVE);
        ConfigurationDB.createConfiguration("Some More Content", Status.INACTIVE);
    }
}
```

## Register Resource in Runtime

将JAX-RS REST注册到服务器的运行时（server’s runtime），你需要`javax.ws.rs.core.Application`扩展，并将其在程序中引入。

```java
package net.restfulapi.app.rest;
 
import java.util.HashSet;
import java.util.Set;
 
import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
 
import net.restfulapi.app.rest.service.ConfigurationResource;
 
@ApplicationPath("/network-management")
public class NetworkApplication extends Application {
 
   private Set<Object> singletons = new HashSet<Object>();
   private Set<Class<?>> empty = new HashSet<Class<?>>();
 
   public NetworkApplication() {
      singletons.add(new ConfigurationResource());
   }
 
   @Override
   public Set<Class<?>> getClasses() {
      return empty;
   }
 
   @Override
   public Set<Object> getSingletons() {
      return singletons;
   }
}
```

在这里`@ApplicationPath`批注将分析该作为REST应用程序分析该类，并自动扫描servlet 3.0容器的进程。它使web.xml文件几乎为空，根本没有REST特定的配置。

## Demo

将该应用程序构建并部署于任何web服务器且启动。现在，可以在任何浏览器中调用上述 REST APIs来进行测试。

##### HTTP GET http://localhost:8080/NetworkManagement/network-management/configurations

获取configuration集合

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/HTTP-GET-Configuration-Collection-Resource.png)

##### HTTP GET http://localhost:8080/NetworkManagement/network-management/configurations/1

获取单个configuration

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/HTTP-GET-Individual-Configuration-Resource.png)

##### HTTP POST http://localhost:8080/NetworkManagement/network-management/configurations

创建新的configuration资源

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/HTTP-POST-Create-New-Resource.png)

##### HTTP PUT http://localhost:8080/NetworkManagement/network-management/configurations/1

更新configuration资源

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/HTTP-PUT-Update-Individual-Configuration-Resource.png)

##### HTTP DELETE http://localhost:8080/NetworkManagement/network-management/configurations/1

删除configuration资源

![](https://github.com/ZhengZhouXu/REST-API/blob/master/img/HTTP-DELETE-Individual-Configuration-Resource.png)



点击下载该应用源码。

> [Download Sourcecode](https://restfulapi.net/wp-content/downloads/NetworkManagement.zip)


