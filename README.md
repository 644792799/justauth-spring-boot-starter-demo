# justauth-spring-boot-starter-demo

> 此 demo 主要演示 Spring Boot 如何使用 justauth-spring-boot-starter 集成 JustAuth

## 快速开始

- 因为暂时没有发布至中央仓库，因此需要体验的童鞋暂时使用我的私服玩儿吧~ 在 `pom.xml` 中添加以下内容

```xml
<repositories>
  <!--阿里云私服-->
  <repository>
    <id>aliyun</id>
    <name>aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public</url>
  </repository>
  <!--xkcoding 私服-->
  <repository>
    <id>xkcoding-nexus</id>
    <name>xkcoding nexus</name>
    <url>https://nexus.xkcoding.com/repository/maven-public/</url>
    <releases>
      <enabled>true</enabled>
    </releases>
    <snapshots>
      <enabled>true</enabled>
    </snapshots>
  </repository>
</repositories>
```

- 引用依赖

```xml
<dependency>
  <groupId>com.xkcoding</groupId>
  <artifactId>justauth-spring-boot-starter</artifactId>
  <version>0.0.1-SNAPSHOT</version>
</dependency>
```

- 添加配置，在 `application.yml` 中添加配置配置信息

```yaml
justauth:
  enabled: true
  type:
    QQ:
      client-id: 10**********6
      client-secret: 1f7d08**********5b7**********29e
      redirect-uri: http://oauth.xkcoding.com/demo/oauth/qq/callback
```

- 然后就开始玩耍吧~

```java
package com.xkcoding.justauthspringbootstarterdemo;

import com.xkcoding.justauth.AuthRequestFactory;
import lombok.RequiredArgsConstructor;
import me.zhyd.oauth.config.AuthSource;
import me.zhyd.oauth.model.AuthCallback;
import me.zhyd.oauth.model.AuthResponse;
import me.zhyd.oauth.request.AuthRequest;
import me.zhyd.oauth.utils.AuthState;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

/**
 * <p>
 * 测试 Controller
 * </p>
 *
 * @author yangkai.shen
 * @date Created in 2019-07-22 11:17
 */
@RestController
@RequestMapping("/oauth")
@RequiredArgsConstructor(onConstructor_ = @Autowired)
public class TestController {
    private final AuthRequestFactory factory;

    @GetMapping("/login/qq")
    public void login(HttpServletResponse response) throws IOException {
        AuthRequest authRequest = factory.get(AuthSource.QQ);
        response.sendRedirect(authRequest.authorize());
    }

    @RequestMapping("/qq/callback")
    public AuthResponse login(AuthCallback callback) {
        AuthRequest authRequest = factory.get(AuthSource.QQ);
        AuthResponse response = authRequest.login(callback);
        // 移除校验通过的state
        AuthState.delete(AuthSource.QQ);
        return response;
    }
}
```

## 附录

`justauth` 配置列表

| 属性名             | 类型                                                         | 默认值 | 可选项     | 描述              |
| ------------------ | ------------------------------------------------------------ | ------ | ---------- | ----------------- |
| `justauth.enabled` | `boolean`                                                    | true   | true/false | 是否启用 JustAuth |
| `justauth.type`    | `java.util.Map<me.zhyd.oauth.config.AuthSource,me.zhyd.oauth.config.AuthConfig>` | 无     |            | JustAuth 配置     |

`justauth.type` 配置列表

| 属性名                      | 描述                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `justauth.type.keys`        | `justauth.type` 是 `Map` 格式的，key 的取值请参考 [`AuthSource`](https://github.com/zhangyd-c/JustAuth/blob/master/src/main/java/me/zhyd/oauth/config/AuthSource.java) |
| `justauth.type.keys.values` | `justauth.type` 是 `Map` 格式的，value 的取值请参考 [`AuthConfig`](https://github.com/zhangyd-c/JustAuth/blob/master/src/main/java/me/zhyd/oauth/config/AuthConfig.java) |

