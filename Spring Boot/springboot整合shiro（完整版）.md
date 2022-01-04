# springboot整合shiro（完整版）

## 转自https://www.jianshu.com/p/7f724bec3dc3

## 1.shiro是什么?

Shiro是Apache下的一个开源项目。shiro属于轻量级框架，相对于SpringSecurity简单的多，也没有SpringSecurity那么复杂。以下是我自己学习之后的记录。

###### 官方架构图如下：

![img](https:////upload-images.jianshu.io/upload_images/15087669-df91fa9f6c3e40d7.png?imageMogr2/auto-orient/strip|imageView2/2/w/414/format/webp)

## 2.主要功能

### shiro主要有三大功能模块：

###### 1. Subject：主体，一般指用户。

###### 2. SecurityManager：安全管理器，管理所有Subject，可以配合内部安全组件。(类似于SpringMVC中的DispatcherServlet)

###### 3. Realms：用于进行权限信息的验证，一般需要自己实现。

## 3.细分功能

###### 1. Authentication：身份认证/登录(账号密码验证)。

###### 2. Authorization：授权，即角色或者权限验证。

###### 3. Session Manager：会话管理，用户登录后的session相关管理。

###### 4. Cryptography：加密，密码加密等。

###### 5. Web Support：Web支持，集成Web环境。

###### 6. Caching：缓存，用户信息、角色、权限等缓存到如redis等缓存中。

###### 7. Concurrency：多线程并发验证，在一个线程中开启另一个线程，可以把权限自动传播过去。

###### 8. Testing：测试支持；

###### 9. Run As：允许一个用户假装为另一个用户（如果他们允许）的身份进行访问。

###### 10. Remember Me：记住我，登录后，下次再来的话不用登录了。

（更多关于shiro是什么的文字请自行去搜索引擎找，本文主要记录springboot与shiro的集成）
 首先先创建springboot项目，此处不过多描述。

#### 上代码：

##### 目录结构：

![img](https://edu-1111.oss-cn-beijing.aliyuncs.com/typora/15087669-b5c72e9970e06528.png)

#### pom.xml:

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>2.0.1</version>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-test</artifactId>
  <scope>test</scope>
</dependency>
<dependency>
  <groupId>org.apache.shiro</groupId>
  <artifactId>shiro-spring</artifactId>
  <version>1.4.0</version>
</dependency>
```

#### user.java(用户实体类)：

```JAVA
package com.wsl.bean;

import java.util.Set;

public class User {
    private String id;
    private String userName;
    private String password;
    /**
     * 用户对应的角色集合
     */
    private Set<Role> roles;
    //省略set、get方法等.....
}
```

#### Role.java(角色对应实体类)：

```JAVA
package com.wsl.bean;

import java.util.Set;

public class Role {

    private String id;
    private String roleName;
    /**
     * 角色对应权限集合
     */
    private Set<Permissions> permissions;
    //省略set、get方法等.....
}
```

#### Permissions.java(权限对应实体类):

```JAVA
public class Permissions {
    private String id;
    private String permissionsName;
    //省略set、get方法等.....
}
```

#### LoginServiceImpl.java:

```JAVA
package com.wsl.service.impl;

import com.wsl.bean.Permissions;
import com.wsl.bean.Role;
import com.wsl.bean.User;
import com.wsl.service.LoginService;
import org.springframework.stereotype.Service;

import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;

@Service
public class LoginServiceImpl implements LoginService {

    @Override
    public User getUserByName(String getMapByName) {
        //模拟数据库查询，正常情况此处是从数据库或者缓存查询。
        return getMapByName(getMapByName);
    }

    /**
     * 模拟数据库查询
     * @param userName
     * @return
     */
    private User getMapByName(String userName){
        //共添加两个用户，两个用户都是admin一个角色，
        //wsl有query和add权限，zhangsan只有一个query权限
        Permissions permissions1 = new Permissions("1","query");
        Permissions permissions2 = new Permissions("2","add");
        Set<Permissions> permissionsSet = new HashSet<>();
        permissionsSet.add(permissions1);
        permissionsSet.add(permissions2);
        Role role = new Role("1","admin",permissionsSet);
        Set<Role> roleSet = new HashSet<>();
        roleSet.add(role);
        User user = new User("1","wsl","123456",roleSet);
        Map<String ,User> map = new HashMap<>();
        map.put(user.getUserName(), user);

        Permissions permissions3 = new Permissions("3","query");
        Set<Permissions> permissionsSet1 = new HashSet<>();
        permissionsSet1.add(permissions3);
        Role role1 = new Role("2","user",permissionsSet1);
        Set<Role> roleSet1 = new HashSet<>();
        roleSet1.add(role1);
        User user1 = new User("2","zhangsan","123456",roleSet1);
        map.put(user1.getUserName(), user1);
        return map.get(userName);
    }
}
```

#### 自定义Realm用于查询用户的角色和权限信息并保存到权限管理器：

#### CustomRealm.java

```JAVA
package com.wsl.shiro;

import com.wsl.bean.Permissions;
import com.wsl.bean.Role;
import com.wsl.bean.User;
import com.wsl.service.LoginService;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.AuthenticationInfo;
import org.apache.shiro.authc.AuthenticationToken;
import org.apache.shiro.authc.SimpleAuthenticationInfo;
import org.apache.shiro.authz.AuthorizationInfo;
import org.apache.shiro.authz.SimpleAuthorizationInfo;
import org.apache.shiro.realm.AuthorizingRealm;
import org.apache.shiro.subject.PrincipalCollection;
import org.springframework.beans.factory.annotation.Autowired;

public class CustomRealm extends AuthorizingRealm {

    @Autowired
    private LoginService loginService;

    @Override
    protected AuthorizationInfo doGetAuthorizationInfo(PrincipalCollection principalCollection) {
        //获取登录用户名
        String name = (String) principalCollection.getPrimaryPrincipal();
        //根据用户名去数据库查询用户信息
        User user = loginService.getUserByName(name);
        //添加角色和权限
        SimpleAuthorizationInfo simpleAuthorizationInfo = new SimpleAuthorizationInfo();
        for (Role role : user.getRoles()) {
            //添加角色
            simpleAuthorizationInfo.addRole(role.getRoleName());
            //添加权限
            for (Permissions permissions : role.getPermissions()) {
                simpleAuthorizationInfo.addStringPermission(permissions.getPermissionsName());
            }
        }
        return simpleAuthorizationInfo;
    }

    @Override
    protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken authenticationToken) throws AuthenticationException {
        //加这一步的目的是在Post请求的时候会先进认证，然后在到请求
        if (authenticationToken.getPrincipal() == null) {
            return null;
        }
        //获取用户信息
        String name = authenticationToken.getPrincipal().toString();
        User user = loginService.getUserByName(name);
        if (user == null) {
            //这里返回后会报出对应异常
            return null;
        } else {
            //这里验证authenticationToken和simpleAuthenticationInfo的信息
            SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(name, user.getPassword().toString(), getName());
            return simpleAuthenticationInfo;
        }
    }
}
```

#### 把CustomRealm和SecurityManager等加入到spring容器：

#### ShiroConfig.java:

```JAVA
package com.wsl.config;

import com.wsl.shiro.CustomRealm;
import org.apache.shiro.mgt.SecurityManager;
import org.apache.shiro.spring.security.interceptor.AuthorizationAttributeSourceAdvisor;
import org.apache.shiro.spring.web.ShiroFilterFactoryBean;
import org.apache.shiro.web.mgt.DefaultWebSecurityManager;
import org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator;
import org.springframework.boot.autoconfigure.condition.ConditionalOnMissingBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import java.util.HashMap;
import java.util.Map;

@Configuration
public class shiroConfig {
    //不加这个注解不生效，具体不详
    @Bean
    @ConditionalOnMissingBean
    public DefaultAdvisorAutoProxyCreator defaultAdvisorAutoProxyCreator() {
        DefaultAdvisorAutoProxyCreator defaultAAP = new DefaultAdvisorAutoProxyCreator();
        defaultAAP.setProxyTargetClass(true);
        return defaultAAP;
    }

    //将自己的验证方式加入容器
    @Bean
    public CustomRealm myShiroRealm() {
        CustomRealm customRealm = new CustomRealm();
        return customRealm;
    }

    //权限管理，配置主要是Realm的管理认证
    @Bean
    public SecurityManager securityManager() {
        DefaultWebSecurityManager securityManager = new DefaultWebSecurityManager();
        securityManager.setRealm(myShiroRealm());
        return securityManager;
    }

    //Filter工厂，设置对应的过滤条件和跳转条件
    @Bean
    public ShiroFilterFactoryBean shiroFilterFactoryBean(SecurityManager securityManager) {
        ShiroFilterFactoryBean shiroFilterFactoryBean = new ShiroFilterFactoryBean();
        shiroFilterFactoryBean.setSecurityManager(securityManager);
        Map<String, String> map = new HashMap<>();
        //登出
        map.put("/logout", "logout");
        //对所有用户认证
        map.put("/**", "authc");
        //登录
        shiroFilterFactoryBean.setLoginUrl("/login");
        //首页
        shiroFilterFactoryBean.setSuccessUrl("/index");
        //错误页面，认证不通过跳转
        shiroFilterFactoryBean.setUnauthorizedUrl("/error");
        shiroFilterFactoryBean.setFilterChainDefinitionMap(map);
        return shiroFilterFactoryBean;
    }

    //加入注解的使用，不加入这个注解不生效
    @Bean
    public AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor(SecurityManager securityManager) {
        AuthorizationAttributeSourceAdvisor authorizationAttributeSourceAdvisor = new AuthorizationAttributeSourceAdvisor();
        authorizationAttributeSourceAdvisor.setSecurityManager(securityManager);
        return authorizationAttributeSourceAdvisor;
    }
}
```

#### LoginController.java:

```JAVA
package com.wsl.controller;

import com.wsl.bean.User;
import org.apache.shiro.SecurityUtils;
import org.apache.shiro.authc.AuthenticationException;
import org.apache.shiro.authc.UsernamePasswordToken;
import org.apache.shiro.authz.AuthorizationException;
import org.apache.shiro.authz.annotation.RequiresPermissions;
import org.apache.shiro.authz.annotation.RequiresRoles;
import org.apache.shiro.subject.Subject;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class LoginController {

    @RequestMapping("/login")
    public String login(User user) {
        //添加用户认证信息
        Subject subject = SecurityUtils.getSubject();
        UsernamePasswordToken usernamePasswordToken = new UsernamePasswordToken(
                user.getUserName(),
                user.getPassword()
        );
        try {
            //进行验证，这里可以捕获异常，然后返回对应信息
            subject.login(usernamePasswordToken);
//            subject.checkRole("admin");
//            subject.checkPermissions("query", "add");
        } catch (AuthenticationException e) {
            e.printStackTrace();
            return "账号或密码错误！";
        } catch (AuthorizationException e) {
            e.printStackTrace();
            return "没有权限";
        }
        return "login success";
    }
     //注解验角色和权限
    @RequiresRoles("admin")
    @RequiresPermissions("add")
    @RequestMapping("/index")
    public String index() {
        return "index!";
    }
}
```

##### 注解验证角色和权限的话无法捕捉异常，从而无法正确的返回给前端错误信息，所以我加了一个类用于拦截异常，具体代码如下

##### MyExceptionHandler.java

```JAVA
package com.wsl.filter;

import lombok.extern.slf4j.Slf4j;
import org.apache.shiro.authz.AuthorizationException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;

@ControllerAdvice
@Slf4j
public class MyExceptionHandler {

    @ExceptionHandler
    @ResponseBody
    public String ErrorHandler(AuthorizationException e) {
        log.error("没有通过权限验证！", e);
        return "没有通过权限验证！";
    }
}
```

打开网页 [http://localhost:8080/login?userName=wsl&password=123456](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2Flogin%3FuserName%3Dwsl%26password%3D123456)

![img](https:////upload-images.jianshu.io/upload_images/15087669-bc8cae7048b8cf1a.png?imageMogr2/auto-orient/strip|imageView2/2/w/346/format/webp)

登录成功



然后输入index地址：[http://localhost:8080/index](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2Findex)

![img](https:////upload-images.jianshu.io/upload_images/15087669-00c9c687c0818516.png?imageMogr2/auto-orient/strip|imageView2/2/w/379/format/webp)

index访问成功



换zhangsan账号登录后再访问index
 [http://localhost:8080/login?userName=zhangsan&password=123456](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2Flogin%3FuserName%3Dzhangsan%26password%3D123456)
 [http://localhost:8080/index](https://links.jianshu.com/go?to=http%3A%2F%2Flocalhost%3A8080%2Findex)

![img](https:////upload-images.jianshu.io/upload_images/15087669-56ec6c1d8e65cd45.png?imageMogr2/auto-orient/strip|imageView2/2/w/605/format/webp)

权限控制访问失败



作者：王诗林
链接：https://www.jianshu.com/p/7f724bec3dc3
来源：简书
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
