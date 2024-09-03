[合集 \- 威哥爱编程(17\)](https://github.com)[1\.35个Redis企业级性能优化点与解决方案06\-25](https://github.com/wgjava/p/18267106)[2\.对比传统数据库，TiDB 强在哪？谈谈 TiDB 的适应场景和产品能力06\-25](https://github.com/wgjava/p/18267457)[3\.深度长文解析SpringWebFlux响应式框架15个核心组件源码07\-04](https://github.com/wgjava/p/18282994)[4\.Nginx性能调优5招35式不可不知的策略实战07\-08](https://github.com/wgjava/p/18289926)[5\.Java Executors类的9种创建线程池的方法及应用场景分析07\-09](https://github.com/wgjava/p/18292258)[6\.Redis数据结构—跳跃表 skiplist 实现源码分析07\-12](https://github.com/wgjava/p/18298064)[7\.Volatile不保证原子性及解决方案07\-19](https://github.com/wgjava/p/18311697)[8\.吃透 JVM 诊断方法与工具使用08\-01](https://github.com/wgjava/p/18336069):[西部世界官网](https://tianchuang88.com)[9\.Java RMI技术详解与案例分析08\-05](https://github.com/wgjava/p/18343209)[10\.通过JUnit源码分析学习编程的奇技淫巧08\-12](https://github.com/wgjava/p/18354483)[11\.什么是依赖倒置原则08\-14](https://github.com/wgjava/p/18359195)[12\.初探 Rust 语言与环境搭建08\-19](https://github.com/wgjava/p/18366810)[13\.为什么用Vite框架？来看它的核心组件案例详解08\-22](https://github.com/wgjava/p/18373550)[14\.Vue状态管理库Pinia详解08\-23](https://github.com/wgjava/p/18375597)[15\.Tomcat的配置文件中有哪些关键的配置项，它们分别有什么作用？08\-26](https://github.com/wgjava/p/18381701)[16\.ECharts实现雷达图详解09\-02](https://github.com/wgjava/p/18392833)17\.OpenFeign深入学习笔记09\-03收起

> OpenFeign 是一个声明式的 Web 服务客户端，它使得编写 Web 服务客户端变得更加容易。OpenFeign 是在 Spring Cloud 生态系统中的一个组件，它整合了 Ribbon（客户端负载均衡器）和 Eureka（服务发现组件），从而简化了微服务之间的调用。


在 SpringCloud 应用中，我们经常会 使用 OpenFeign，比如通过定义一个接口并使用注解的方式来创建一个 Web 服务客户端，而不需要编写大量的模板代码。OpenFeign 会自动生成接口的实现类，并使用 Ribbon 来调用相应的服务。


我们先来上手用一下，在 Spring Cloud 项目中使用 OpenFeign：


**需求**：我们的业务场景是这样的：一个电子商务平台，其中包含一个商品服务（`product-service`）和一个订单服务（`order-service`）。我们要使用 OpenFeign 来实现订单服务调用商品服务的接口。


### 步骤1：创建商品服务（product\-service）


1. **添加依赖**（`pom.xml`）：



```
   <dependencies>
       
       <dependency>
           <groupId>org.springframework.bootgroupId>
           <artifactId>spring-boot-starter-webartifactId>
       dependency>
       
       <dependency>
           <groupId>org.springframework.bootgroupId>
           <artifactId>spring-boot-starter-actuatorartifactId>
       dependency>
   dependencies>

```

2. **主应用类**（`ProductApplication.java`）：



```
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class ProductApplication {
       public static void main(String[] args) {
           SpringApplication.run(ProductApplication.class, args);
       }
   }

```

3. **商品控制器**（`ProductController.java`）：



```
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   public class ProductController {

       @GetMapping("/products/{id}")
       public String getProduct(@PathVariable("id") Long id) {
           // 模拟数据库中获取商品信息
           return "Product with ID: " + id;
       }
   }

```

### 步骤2：创建订单服务（order\-service）


1. **添加依赖**（`pom.xml`）：



```
   <dependencies>
       
       <dependency>
           <groupId>org.springframework.bootgroupId>
           <artifactId>spring-boot-starter-webartifactId>
       dependency>
       
       <dependency>
           <groupId>org.springframework.cloudgroupId>
           <artifactId>spring-cloud-starter-openfeignartifactId>
       dependency>
   dependencies>

```

2. **主应用类**（`OrderApplication.java`）：



```
   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;
   import org.springframework.cloud.openfeign.EnableFeignClients;

   @SpringBootApplication
   @EnableFeignClients
   public class OrderApplication {
       public static void main(String[] args) {
           SpringApplication.run(OrderApplication.class, args);
       }
   }

```

3. **Feign 客户端接口**（`ProductClient.java`）：



```
   import org.springframework.cloud.openfeign.FeignClient;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;

   @FeignClient(name = "product-service", url = "http://localhost:8081")
   public interface ProductClient {
       @GetMapping("/products/{id}")
       String getProduct(@PathVariable("id") Long id);
   }

```

4. **订单控制器**（`OrderController.java`）：



```
   import org.springframework.beans.factory.annotation.Autowired;
   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.PathVariable;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   public class OrderController {

       private final ProductClient productClient;

       @Autowired
       public OrderController(ProductClient productClient) {
           this.productClient = productClient;
       }

       @GetMapping("/orders/{id}/product")
       public String getOrderProduct(@PathVariable("id") Long id) {
           // 调用商品服务获取商品信息
           return productClient.getProduct(id);
       }
   }

```

### 步骤3：运行和测试


1. **启动商品服务**（`ProductApplication`）：


	* 运行 `ProductApplication` 的 `main` 方法。
2. **启动订单服务**（`OrderApplication`）：


	* 运行 `OrderApplication` 的 `main` 方法。
3. **测试调用**：


	* 使用浏览器或 Postman 访问 `http://localhost:8082/orders/1/product`，我们就能看到商品服务返回的商品信息。


以上是OpenFeign的基本使用，作为优秀的程序员，我们必须要去深入了解OpenFeign核心组件背后的实现，知己知彼，方能百战不殆。下面我们来一起看下 OpenFeign 的一些核心组件及其源码分析：


**OpenFeign 的核心组件有哪些？**


OpenFeign 是 Spring Cloud 生态系统中的一个声明式 Web 服务客户端，用于简化微服务之间的 HTTP 调用。


## 1\. Encoder：


在 OpenFeign 中，`Encoder` 组件负责将请求数据序列化成可以发送的格式。默认情况下，OpenFeign 只支持将请求数据序列化为字符串或字节数组。如果需要支持更复杂的对象序列化，可以通过实现自定义的 `Encoder` 来实现。


我们来分析一下 `Encoder` 组件的源码实现：


### 步骤1：定义 Encoder 接口


首先，Feign定义了一个 `Encoder` 接口，该接口包含一个 `encode` 方法，用于将对象序列化为字节数组：



```
public interface Encoder {
    void encode(Object object, Type bodyType, RequestTemplate template) throws EncodeException;
}

```

* `object`：要序列化的对象。
* `bodyType`：对象的类型信息，通常用于确定如何序列化对象。
* `template`：`RequestTemplate` 对象，用于设置请求的主体（body）。


### 步骤2：实现默认 Encoder


OpenFeign 提供了一个默认的 `Encoder` 实现，通常使用 Jackson 或其他 JSON 库来序列化对象为 JSON 格式：



```
public class JacksonEncoder extends SpringEncoder implements Encoder {
    public JacksonEncoder(ObjectFactory objectFactory) {
        super(objectFactory);
    }

    @Override
    public void encode(Object object, Type bodyType, RequestTemplate template) throws EncodeException {
        // 将对象序列化为 JSON 格式，并设置到 RequestTemplate 中
        byte[] body = this.objectFactory.createInstance(bodyType).writeValueAsBytes(object);
        template.body(body);
        template.requestBody(Request.Body.create(body, ContentType.APPLICATION_JSON));
    }
}

```

在这个实现中，`JacksonEncoder` 使用 Jackson 库将对象序列化为 JSON，并设置请求的内容类型为 `APPLICATION_JSON`。


### 步骤3：自定义 Encoder 实现


如果我们需要支持其他类型的序列化，可以创建自定义的 `Encoder` 实现。例如，如果要支持 XML 序列化，可以创建一个使用 JAXB 或其他 XML 库的 `Encoder`：



```
public class XmlEncoder implements Encoder {
    @Override
    public void encode(Object object, Type bodyType, RequestTemplate template) throws EncodeException {
        // 使用 XML 库将对象序列化为 XML 格式
        String xml = serializeToXml(object);
        template.body(xml, ContentType.APPLICATION_XML);
    }

    private String serializeToXml(Object object) {
        // 实现对象到 XML 的序列化逻辑
        // ...
        return xml;
    }
}

```

### 步骤4：配置 OpenFeign 客户端使用自定义 Encoder


在 Feign 客户端配置中，可以指定自定义的 `Encoder` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Encoder encoder() {
        return new XmlEncoder();
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


`Encoder` 接口，为请求数据的序列化提供了一个扩展点。OpenFeign 提供了默认的 `JacksonEncoder` 实现，它使用 Jackson 库来序列化对象为 JSON 格式。如果想实现自定义的 `Encoder`，来支持其他数据格式，如 XML、Protobuf 等也是非常方便灵活的。


## 2\. Decoder：


OpenFeign 的`Decoder` 组件负责将HTTP响应体（通常是字节流）反序列化为Java对象。默认情况下，OpenFeign支持将响应体反序列化为字符串或字节数组。如果需要支持更复杂的对象反序列化，可以通过实现自定义的 `Decoder` 来实现。


下面来分析 `Decoder` 组件的源码实现：


### 步骤1：定义 Decoder 接口


OpenFeign 定义了一个 `Decoder` 接口，该接口包含一个 `decode` 方法，用于将响应体反序列化为对象：



```
public interface Decoder {
     T decode(Response response, Type type) throws IOException, DecodeException;
}

```

* `T`：要反序列化成的对象类型。
* `response`：Feign的 `Response` 对象，包含了HTTP响应的所有信息。
* `type`：要反序列化成的对象的类型信息。


### 步骤2：实现默认 Decoder


OpenFeign 提供了一个默认的 `Decoder` 实现，通常使用 Jackson 或其他 JSON 库来反序列化JSON响应体：



```
public class JacksonDecoder extends SpringDecoder implements Decoder {
    public JacksonDecoder(ObjectFactory objectFactory) {
        super(objectFactory);
    }

    @Override
    public  T decode(Response response, Type type) throws IOException, DecodeException {
        // 从响应中获取字节流
        InputStream inputStream = response.body().asInputStream();
        // 使用 Jackson 库将字节流反序列化为 Java 对象
        return this.objectFactory.createInstance(type).readValue(inputStream, type);
    }
}

```

在这个实现中，`JacksonDecoder` 使用 Jackson 库将响应体的字节流反序列化为 Java 对象，并根据响应的状态码抛出相应的异常或返回对象。


### 步骤3：自定义 Decoder 实现


如果咱们需要支持其他类型的反序列化，可以创建自定义的 `Decoder` 实现。比如要支持 XML 反序列化，可以创建一个使用 JAXB 或其他 XML 库的 `Decoder`：



```
public class XmlDecoder implements Decoder {
    @Override
    public  T decode(Response response, Type type) throws IOException, DecodeException {
        // 从响应中获取字节流
        InputStream inputStream = response.body().asInputStream();
        // 使用 XML 库将字节流反序列化为 Java 对象
        T object = deserializeFromXml(inputStream, type);
        return object;
    }

    private  T deserializeFromXml(InputStream inputStream, Type type) {
        // 实现 XML 到 Java 对象的反序列化逻辑
        // ...
        return null;
    }
}

```

### 步骤4：配置 OpenFeign 客户端使用自定义 Decoder


在 Feign 客户端配置中，可以指定自定义的 `Decoder` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Decoder decoder() {
        return new XmlDecoder();
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


OpenFeign 提供了默认的 `JacksonDecoder` 实现，它使用 Jackson 库来反序列化 JSON 格式的响应体。咱们还可以通过实现自定义的 `Decoder`，可以支持其他数据格式，如 XML等。开发中，咱们可以根据需要选择或实现适合自己业务场景的反序列化方式。


## 3\. Contract：


OpenFeign的`Contract` 组件负责将接口的方法和注解转换为HTTP请求。它定义了如何将Java接口映射到HTTP请求上，包括请求的URL、HTTP方法、请求头、查询参数和请求体等。`Contract` 组件是Feign中非常核心的部分，因为它决定了Feign客户端如何理解和构建HTTP请求。


### 步骤1：定义 Contract 接口


Feign定义了一个 `Contract` 接口，该接口包含两个主要的方法：



```
public interface Contract {
    List parseAndValidatteMethods(FeignTarget target);
    RequestTemplate create(Request request, Target target, Method method, Object... argv);
}

```

* `parseAndValidatteMethods`：解析并验证目标接口的方法，生成 `MethodMetadata` 列表，每个 `MethodMetadata` 包含一个方法的所有元数据。
* `create`：根据 `Request` 和 `Target` 创建 `RequestTemplate`，用于构建实际的HTTP请求。


### 步骤2：实现默认 Contract


Feign提供了一个默认的 `Contract` 实现，通常使用Java的反射API来解析接口的方法和注解：



```
public class FeignContract implements Contract {
    @Override
    public List parseAndValidateMethods(FeignTarget target) {
        // 解析目标接口的方法，生成 MethodMetadata 列表
        // ...
    }

    @Override
    public RequestTemplate create(Request request, Target target, Method method, Object... argv) {
        // 根据 MethodMetadata 和参数创建 RequestTemplate
        // ...
    }
}

```

在实现中咱们可以看到，`FeignContract` 会检查接口方法上的注解（如 `@GetMapping`、`@PostMapping` 等），并根据这些注解构建HTTP请求。


### 步骤3：自定义 Contract 实现


同样的道理，如果需要支持自定义的注解或扩展Feign的功能，可以通过实现自定义的 `Contract` 来实现：



```
public class MyCustomContract implements Contract {
    @Override
    public List parseAndValidateMethods(FeignTarget target) {
        // 自定义解析逻辑
        // ...
    }

    @Override
    public RequestTemplate create(Request request, Target target, Method method, Object... argv) {
        // 自定义创建 RequestTemplate 的逻辑
        // ...
    }
}

```

### 步骤4：配置 OpenFeign 客户端使用自定义 Contract


在Feign客户端配置中，可以指定自定义的 `Contract` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Contract contract() {
        return new MyCustomContract();
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


OpenFeign 提供了默认的 `FeignContract` 实现，它使用Java的反射API来解析接口的方法和注解，并生成 `MethodMetadata`。这种方式允许Feign自动处理标准的JAX\-RS注解。咱们可以通过实现自定义的 `Contract`，可以支持自定义注解或改变Feign的请求构建逻辑。


## 4\. Client：


`Client` 组件是负责发送HTTP请求并接收HTTP响应的核心组件。OpenFeign 默认使用Java标准库中的`HttpURLConnection`来发送请求，但也支持使用其他HTTP客户端库，如Apache HttpClient或OkHttp。


### 步骤1：定义 Client 接口


OpenFeign中定义了一个`Client`接口，该接口包含一个`execute`方法，用于执行HTTP请求：



```
public interface Client {
    Response execute(Request request, Request.Options options) throws IOException;
}

```

* `Request`：代表要执行的HTTP请求。
* `Request.Options`：包含请求的配置选项，如连接超时和读取超时。
* `Response`：代表HTTP响应。


### 步骤2：实现默认 Client


OpenFeign 提供了一个默认的`Client`实现，使用Java的`HttpURLConnection`：



```
public class HttpURLConnectionClient implements Client {
    @Override
    public Response execute(Request request, Request.Options options) throws IOException {
        // 创建和配置 HttpURLConnection
        URL url = new URL(request.url());
        HttpURLConnection connection = (HttpURLConnection) url.openConnection();
        // 设置请求方法、头信息、超时等
        // ...
        // 发送请求并获取响应
        // ...
        return response;
    }
}

```

在这个实现中，`HttpURLConnectionClient`使用`HttpURLConnection`来构建和发送HTTP请求，并处理响应。


### 步骤3：自定义 Client 实现


如果我们需要使用其他HTTP客户端库，可以创建自定义的`Client`实现。例如，使用Apache HttpClient：



```
public class HttpClientClient implements Client {
    private final CloseableHttpClient httpClient;

    public HttpClientClient(CloseableHttpClient httpClient) {
        this.httpClient = httpClient;
    }

    @Override
    public Response execute(Request request, Request.Options options) throws IOException {
        // 使用 Apache HttpClient 构建和发送HTTP请求
        // ...
        return response;
    }
}

```

### 步骤4：配置 Feign 客户端使用自定义 Client


在Feign配置中，可以指定自定义的`Client`实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Client httpClientClient() {
        CloseableHttpClient httpClient = HttpClients.createDefault();
        return new HttpClientClient(httpClient);
    }
}

```

然后在`@FeignClient`注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


OpenFeign 提供了默认的`HttpURLConnectionClient`实现，它使用Java标准库中的`HttpURLConnection`来发送请求。这种方式的好处是简单且无需额外依赖。也可以通过实现自定义的`Client`，如Apache HttpClient或OkHttp。这让OpenFeign能够适应不同的性能和功能需求。


## 5\. RequestInterceptor：


`RequestInterceptor` 是一个非常重要的组件，它允许咱们在请求发送之前对其进行拦截，从而可以添加一些通用的处理逻辑，比如设置认证头、日志记录、修改请求参数等。我们来分析一下 `RequestInterceptor` 组件的源码实现：


### 步骤1：定义 RequestInterceptor 接口


Feign定义了一个 `RequestInterceptor` 接口，该接口包含一个 `apply` 方法，用于在请求发送前对 `RequestTemplate` 进行操作：



```
public interface RequestInterceptor {
    void apply(RequestTemplate template);
}

```

* `RequestTemplate`：代表即将发送的HTTP请求，可以修改URL、头信息、请求体等。


### 步骤2：实现默认 RequestInterceptor


OpenFeign 提供了一些默认的 `RequestInterceptor` 实现，例如用于设置默认头信息的 `RequestInterceptor`：



```
public class DefaultRequestInterceptor implements RequestInterceptor {
    private final Configuration configuration;

    public DefaultRequestInterceptor(Configuration configuration) {
        this.configuration = configuration;
    }

    @Override
    public void apply(RequestTemplate template) {
        // 设置默认的头信息，比如Content-Type
        template.header("Content-Type", configuration.getContentType().toString());
        // 可以添加更多的默认处理逻辑
    }
}

```

在这个实现中，`DefaultRequestInterceptor` 会在每个请求中设置一些默认的头信息。


### 步骤3：自定义 RequestInterceptor 实现


咱们可以根据需要实现自定义的 `RequestInterceptor`。例如，添加一个用于设置认证头的拦截器：



```
public class AuthRequestInterceptor implements RequestInterceptor {
    private final String authToken;

    public AuthRequestInterceptor(String authToken) {
        this.authToken = authToken;
    }

    @Override
    public void apply(RequestTemplate template) {
        // 在每个请求中添加认证头
        template.header("Authorization", "Bearer " + authToken);
    }
}

```

### 步骤4：配置 OpenFeign 客户端使用自定义 RequestInterceptor


在 OpenFeign 配置中，可以指定自定义的 `RequestInterceptor` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public RequestInterceptor authRequestInterceptor() {
        // 假设从配置文件或环境变量中获取认证令牌
        String authToken = "your-auth-token";
        return new AuthRequestInterceptor(authToken);
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


RequestInterceptor让咱们在不修改每个单独请求的情况下，统一处理请求。这使得Feign客户端更加灵活和强大，能够适应各种复杂的业务需求。


## 6\. Retryer：


`Retryer` 组件负责定义重试策略，它决定了在遇到特定类型的错误时是否重试请求，以及重试的次数和间隔。`Retryer` 是Feign中的一个重要组件，特别是在网络不稳定或服务不稳定的环境中，大派用场，它可以显著提高系统的健壮性哦。


### 步骤1：定义 Retryer 接口


Feign定义了一个 `Retryer` 接口，该接口包含几个关键的方法，用于控制重试的行为：



```
public interface Retryer {
    void continueOrPropagate(RetryableException e);
    Retryer clone();
    long getDelay(RetryableException e, int attempt);
    boolean shouldRetry(RetryableException e, int attempt, int retry);
}

```

* `continueOrPropagate`：决定是继续重试还是抛出异常。
* `clone`：创建 `Retryer` 的副本，通常用于每个请求的独立重试策略。
* `getDelay`：返回在下一次重试之前的延迟时间。
* `shouldRetry`：决定是否应该重试请求。


### 步骤2：实现默认 Retryer


Feign提供了一个默认的 `Retryer` 实现，通常是一个简单的重试策略，例如：



```
public class DefaultRetryer implements Retryer {
    private final long period;
    private final long maxPeriod;
    private final int maxAttempts;

    public DefaultRetryer(long period, long maxPeriod, int maxAttempts) {
        this.period = period;
        this.maxPeriod = maxPeriod;
        this.maxAttempts = maxAttempts;
    }

    @Override
    public void continueOrPropagate(RetryableException e) {
        // 根据异常类型和重试次数决定是否重试
        if (shouldRetry(e, e.getAttempt(), maxAttempts)) {
            // 继续重试
        } else {
            // 抛出异常
            throw e;
        }
    }

    @Override
    public Retryer clone() {
        return new DefaultRetryer(period, maxPeriod, maxAttempts);
    }

    @Override
    public long getDelay(RetryableException e, int attempt) {
        // 计算重试延迟
        return Math.min(period * (long) Math.pow(2, attempt), maxPeriod);
    }

    @Override
    public boolean shouldRetry(RetryableException e, int attempt, int retry) {
        // 根据异常类型和重试次数决定是否重试
        return attempt < retry;
    }
}

```

在这个实现中，`DefaultRetryer` 使用指数退避策略来计算重试延迟，并允许指定最大重试次数。


### 步骤3：自定义 Retryer 实现


当咱们需要更复杂的重试策略时，可以创建自定义的 `Retryer` 实现。例如，可以基于特定的异常类型或响应码来决定重试策略：



```
public class CustomRetryer implements Retryer {
    // ... 自定义重试逻辑 ...

    @Override
    public void continueOrPropagate(RetryableException e) {
        // 自定义重试逻辑
    }

    @Override
    public Retryer clone() {
        return new CustomRetryer();
    }

    @Override
    public long getDelay(RetryableException e, int attempt) {
        // 自定义延迟逻辑
        return ...;
    }

    @Override
    public boolean shouldRetry(RetryableException e, int attempt, int retry) {
        // 自定义重试条件
        return ...;
    }
}

```

### 步骤4：配置 Feign 客户端使用自定义 Retryer


在Feign配置中，可以指定自定义的 `Retryer` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Retryer retryer() {
        return new CustomRetryer();
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


OpenFeign 允许我们根据需要选择或实现适合自己业务场景的重试策略，从而提高系统的健壮性和可靠性。问题来了，啥是指数退避策略？


解释一下哈，指数退避策略（Exponential Backoff）是一种在网络通信和分布式系统中常用的重试策略，特别是在处理临时故障或网络延迟时。这种策略旨在通过增加连续重试之间的等待时间来减少系统的负载，并提高重试成功的机会。


指数退避策略的工作原理是这样的：当发生错误或故障时，系统首先会等待一个初始的短暂延迟，然后重试。如果第一次重试失败，等待时间会指数增长。这意味着每次重试的等待时间都是前一次的两倍（或另一个指数因子）。


通常会设置一个最大尝试次数，以防止无限重试。为了避免等待时间过长，会设定一个最大延迟时间，超过这个时间后，即使重试次数没有达到最大次数，也不会再增加等待时间。


为了减少多个客户端同时重试时的同步效应，有时会在指数退避中加入随机化因子，使得每次的等待时间在一定范围内变化。


## 7\. Configuration：


`Configuration` 组件是一个关键的设置类，它允许用户自定义Feign客户端的行为。`Configuration` 类通常包含了一系列的设置，比如连接超时、读取超时、重试策略、编码器、解码器、契约（Contract）、日志级别等。这些设置可以应用于所有的Feign客户端，或者特定的Feign客户端。


### 步骤1：定义 Configuration 接口


Feign定义了一个 `Configuration` 接口，该接口允许用户配置Feign客户端的各种参数：



```
public interface Configuration {
    // 返回配置的编码器
    Encoder encoder();

    // 返回配置的解码器
    Decoder decoder();

    // 返回配置的契约
    Contract contract();

    // 返回配置的请求拦截器
    RequestInterceptor requestInterceptor();

    // 返回配置的重试策略
    Retryer retryer();

    // 返回配置的日志级别
    Logger.Level loggerLevel();
    
    // ... 可能还有其他配置方法 ...
}

```

### 步骤2：实现默认 Configuration


Feign提供了一个默认的 `Configuration` 实现，这个实现包含了Feign的默认行为：



```
public class DefaultConfiguration implements Configuration {
    // ... 定义默认的编码器、解码器、契约等 ...

    @Override
    public Encoder encoder() {
        return new JacksonEncoder(...);
    }

    @Override
    public Decoder decoder() {
        return new JacksonDecoder(...);
    }

    @Override
    public Contract contract() {
        return new SpringMvcContract(...);
    }

    // ... 实现其他配置方法 ...
}

```

在这个实现中，`DefaultConfiguration` 定义了Feign的默认编码器、解码器、契约等组件。


### 步骤3：自定义 Configuration 实现


用户可以创建自定义的 `Configuration` 实现，以覆盖默认的行为：



```
public class CustomConfiguration extends DefaultConfiguration {
    // ... 自定义特定的配置 ...

    @Override
    public Encoder encoder() {
        // 返回自定义的编码器
        return new CustomEncoder(...);
    }

    @Override
    public Decoder decoder() {
        // 返回自定义的解码器
        return new CustomDecoder(...);
    }

    // ... 可以覆盖其他配置方法 ...
}

```

### 步骤4：配置 Feign 客户端使用自定义 Configuration


在Feign配置中，可以指定自定义的 `Configuration` 实现：



```
@Configuration
public class FeignConfig {
    @Bean
    public Configuration feignConfiguration() {
        return new CustomConfiguration(...);
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

## 8\. Target：


`Target` 组件代表了Feign客户端将要调用的远程服务的目标。它通常包含了服务的名称和可能的特定配置，例如请求的URL。`Target` 组件在Feign的动态代理机制中扮演着重要角色，因为它定义了如何将方法调用转换为实际的HTTP请求。


### 步骤1：定义 Target 接口


Feign定义了一个 `Target` 接口，该接口包含一些关键的方法和属性：



```
public interface Target {
    String name();

    String url();

    Class type();
}

```

* `name()`：返回服务的名称，通常用于服务发现。
* `url()`：返回服务的URL，可以是完整的URL或者是一个模板。
* `type()`：返回Feign客户端接口的类型。


### 步骤2：实现 Target 接口


Feign提供了 `Target` 接口的实现，通常是一个名为 `HardCodedTarget` 的类：



```
public class HardCodedTarget implements Target {
    private final String name;
    private final String url;
    private final Class type;

    public HardCodedTarget(Class type, String name, String url) {
        this.type = type;
        this.name = name;
        this.url = url;
    }

    @Override
    public String name() {
        return name;
    }

    @Override
    public String url() {
        return url;
    }

    @Override
    public Class type() {
        return type;
    }
}

```

在这个实现中，`HardCodedTarget` 通过构造函数接收服务的名称、URL和接口类型，并提供相应的getter方法。


### 步骤3：使用 Target 组件


在Feign客户端接口中，可以通过 `@FeignClient` 注解的 `value` 属性指定服务名称，Feign在内部会使用 `Target` 来构建代理：



```
@FeignClient(value = "myService", url = "http://localhost:8080")
public interface MyClient extends MyServiceApi {
    // 定义服务方法
}

```

Feign会根据注解信息创建一个 `HardCodedTarget` 实例，并使用它来构建动态代理。


### 步骤4：动态代理和 Target


Feign使用Java的动态代理机制（是不是哪哪都是动态代理，所以说动态代理很重要）来创建客户端代理。在Feign的 `ReflectiveFeign` 类中，会使用 `InvocationHandlerFactory` 来创建 `InvocationHandler`：



```
public class ReflectiveFeign extends Feign {
    private final InvocationHandlerFactory invocationHandlerFactory;

    public ReflectiveFeign(InvocationHandlerFactory invocationHandlerFactory) {
        this.invocationHandlerFactory = invocationHandlerFactory;
    }

    @Override
    public  T newInstance(Target target) {
        // 使用 InvocationHandlerFactory 创建 InvocationHandler
        InvocationHandler invocationHandler = invocationHandlerFactory.create(target);
        // 创建动态代理
        return (T) Proxy.newProxyInstance(target.type().getClassLoader(),
                new Class[]{target.type()}, invocationHandler);
    }
}

```

在这个过程中，`InvocationHandler` 会使用 `Target` 来构建实际的HTTP请求。


## 9\. InvocationHandlerFactory：


`InvocationHandlerFactory` 组件是动态代理的核心，它负责创建 `InvocationHandler`，这是Java动态代理机制的关键部分。`InvocationHandler` 定义了代理对象在被调用时的行为。在Feign的上下文中，`InvocationHandler` 负责将方法调用转换为HTTP请求。


### 步骤1：定义 InvocationHandlerFactory 接口


Feign定义了一个 `InvocationHandlerFactory` 接口，该接口包含一个方法，用于创建 `InvocationHandler`：



```
public interface InvocationHandlerFactory {
    InvocationHandler create(Target target);
}

```

* `Target`：代表Feign客户端的目标，包含了服务的名称、URL和接口类型。


### 步骤2：实现 InvocationHandlerFactory


Feign提供了一个默认的 `InvocationHandlerFactory` 实现，通常是一个名为 `ReflectiveInvocationHandlerFactory` 的类：



```
public class ReflectiveInvocationHandlerFactory implements InvocationHandlerFactory {
    @Override
    public InvocationHandler create(Target target) {
        return new ReflectiveInvocationHandler(target);
    }
}

```

在这个实现中，`ReflectiveInvocationHandlerFactory` 创建了一个 `ReflectiveInvocationHandler` 实例，这个 `InvocationHandler` 会处理反射调用。


### 步骤3：实现 InvocationHandler


`ReflectiveInvocationHandler` 是 `InvocationHandler` 接口的一个实现，它负责将方法调用转换为HTTP请求：



```
public class ReflectiveInvocationHandler implements InvocationHandler {
    private final Target target;
    private final FeignClientFactory feignClientFactory;

    public ReflectiveInvocationHandler(Target target, FeignClientFactory feignClientFactory) {
        this.target = target;
        this.feignClientFactory = feignClientFactory;
    }

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        // 根据方法和参数构建RequestTemplate
        RequestTemplate template = buildTemplateFromArgs(target, method, args);
        // 发送请求并获取响应
        Response response = feignClientFactory.create(target).execute(template, options());
        // 根据响应构建返回值
        return decode(response, method.getReturnType());
    }

    private RequestTemplate buildTemplateFromArgs(Target target, Method method, Object[] args) {
        // 构建请求模板逻辑
        // ...
    }

    private Response.Options options() {
        // 获取请求选项，如超时设置
        // ...
    }

    private Object decode(Response response, Type type) {
        // 将响应解码为方法返回类型的逻辑
        // ...
    }
}

```

代码中我们发现，`invoke` 方法是核心，它根据目标对象、方法和参数构建一个 `RequestTemplate`，然后使用 `FeignClient` 发送请求并获取响应。


### 步骤4：配置 Feign 客户端使用自定义 InvocationHandlerFactory


自定义 `InvocationHandlerFactory`，可以在Feign配置中指定：



```
@Configuration
public class FeignConfig {
    @Bean
    public InvocationHandlerFactory invocationHandlerFactory() {
        return new CustomInvocationHandlerFactory();
    }
}

```

然后在 `@FeignClient` 注解中指定配置类：



```
@FeignClient(name = "myClient", configuration = FeignConfig.class)
public interface MyClient {
    // ...
}

```

### 小结一下


为啥说程序员需要充分理解设计模式的应用，如果在面试时问你任何关于设计模式的问题，请不要只讲概念、不要只讲概念、不要只讲概念，还要结合业务场景，或者结合框架源码的理解来讲，讲一讲解决了什么问题，这是关键所在。


## 最后


OpenFeign 是 Spring Cloud 生态系统中的一个强大工具，它使得微服务之间的通信变得更加简单和高效。通过使用 OpenFeign，开发者可以专注于业务逻辑的实现，而不需要关心底层的 HTTP 通信细节。欢迎关注威哥爱编程，原创不易，求个赞啊兄弟。


