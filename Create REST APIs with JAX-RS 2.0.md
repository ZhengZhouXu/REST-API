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


