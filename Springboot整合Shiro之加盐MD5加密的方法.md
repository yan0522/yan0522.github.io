# Springboot整合Shiro之加盐MD5加密的方法

## **1.自定义realm，在Shiro的配置类中加入以下bean**

```java
//将自己的验证方式加入容器
@Bean
public CustomRealm myShiroRealm() {
CustomRealm customRealm = new CustomRealm();
return customRealm;
}
```

## **2.重写方法**

```java
/**
* 认证
* @param token
* @return
* @throws AuthenticationException
*/
@Override
protected AuthenticationInfo doGetAuthenticationInfo(AuthenticationToken token) throws AuthenticationException {
//加这一步的目的是在Post请求的时候会先进认证，然后在到请求
    if (token.getPrincipal() == null) {
    return null;
    }
    //获取用户信息
    UsernamePasswordToken usernamePasswordToken = (UsernamePasswordToken) token;
    String name = usernamePasswordToken.getUsername();
    //上下两种都可以
    //String name = token.getPrincipal().toString();
    User user = userService.getUserByName(name);
    if (user == null) {
    //这里返回后会报出对应异常
    throw new UnknownAccountException("用户名[" + name + "]不存在!");
    }else{
    //这里验证authenticationToken和simpleAuthenticationInfo的信息
    Object principal = user;
    Object credentials = user.getPassword();
    String realName = getName();
    ByteSource salt = ByteSource.Util.bytes(name);
    SimpleAuthenticationInfo simpleAuthenticationInfo = new SimpleAuthenticationInfo(principal,credentials, salt,realName);
    return simpleAuthenticationInfo;
    }
}
```

## **3.你还需要告诉shiro你是经过加密的，在Config内新建如下bean**

```java
/**
* shiro的在进行密码验证的时候，将会在此进行匹配
* @return
*/
@Bean("hashedCredentialsMatcher")
public HashedCredentialsMatcher hashedCredentialsMatcher() {
HashedCredentialsMatcher hashedCredentialsMatcher = new HashedCredentialsMatcher();
// 散列算法:这里使用MD5算法;
hashedCredentialsMatcher.setHashAlgorithmName("md5");
// 散列的次数，比如散列两次，相当于md5(md5(""));
hashedCredentialsMatcher.setHashIterations(1024);
//表示是否存储散列后的密码为16进制，需要和生成密码时的一样，默认是base64;
//hashedCredentialsMatcher.setStoredCredentialsHexEncoded(true)
return hashedCredentialsMatcher;
}
```

## **4.并注册**

```java
//将自己的验证方式加入容器
@Bean
public CustomRealm myShiroRealm() {
CustomRealm customRealm = new CustomRealm();
// 配置 加密 （在加密后，不配置的话会导致登陆密码失败）
customRealm.setCredentialsMatcher(hashedCredentialsMatcher());
return customRealm;
}
```

