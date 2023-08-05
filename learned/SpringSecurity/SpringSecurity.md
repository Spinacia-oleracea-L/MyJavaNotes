# 基本概念

## 一、认证与授权

### 认证：authentication

所谓“认证” ，解决的是“你是谁”这⼀ 问题。也就是说，对于每次访问请求，系统都能判断出访问者是否具有 合法的身份标识。

### 授权：authorization

⼀旦明确“你是谁”，下⼀步就可以判断“你能做什么” ，这个步骤就 是“授权”。通⽤的授权模型是基于权限管理体系的，也就是说，授权是对资源、权限、⻆⾊和⽤户的⼀种组合处理。

## 二、用户对象与认证对象

### 用户对象：

Spring Security 中的⽤户对象⽤来描述⽤户并完成对⽤户信息的管理，涉及 UserDetails、GrantedAuthority、UserDetailsService和 UserDetailsManager这4个核⼼⽤户对象。

* UserDetails：描述Spring Security中的⽤户。 

  ```java
  // UserDetails-⽤户详细接⼝
  public interface UserDetails extends Serializable {
      
      //获取该⽤户的权限信息
      Collection<? extends GrantedAuthority> getAuthorities();
      
      //获取密码
      String getPassword();
      
      //获取⽤户名
      String getUsername();
      
      //判断该账户是否已失效
      boolean isAccountNonExpired();
      
      //判断该账户是否已被锁定
      boolean isAccountNonLocked();
      
      //判断该账户的凭证信息是否已失效
      boolean isCredentialsNonExpired();
      
      //判断该⽤户是否可⽤
      boolean isEnabled();
      
  }
  ```

* GrantedAuthority：定义⽤户所能执⾏的操作权限。

  ```java
  // GrantedAuthority-⽤户拥有权限接⼝。UserDetails的第一项属性
  public interface GrantedAuthority extends Serializable {
      
      //获取权限信息
      String getAuthority();
      
  }
  ```

* UserDetailsService：定义对UserDetails的查询操作。

  ```java
  // UserDetailsService-⽤户查询操作
  public interface UserDetailsService {
      
      //根据⽤户名获取⽤户信息
      UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
      
  }
  ```

* UserDetailsManager：扩展UserDetailsService，添加创建⽤户、 修改⽤户密码等功能。

  ```java
  // UserDetailsManager-⽤户CRUD操作
  public interface UserDetailsManager extends UserDetailsService {
      
      //创建⽤户
      void createUser(UserDetails user);
      
      //更新⽤户
      void updateUser(UserDetails user);
      
      //删除⽤户
      void deleteUser(String username);
      
      //修改密码
      void changePassword(String oldPassword, String newPassword);
      
      //判断指定⽤户名的⽤户是否存在
      boolean userExists(String username);
      
  }
  ```

### 认证对象：

有了⽤户对象，接下来我们就可以讨论具体的认证实现过程了。

* Authentication-认证请求详细信息：代表认证**请求**本身， 并保存该请求访问应⽤程序过程中所涉及的各个实体的详细信息。在安全领域，对Web应⽤程序发起访问请求的⽤户通常被称为主体（principal）。

  ```java
  public interface Authentication extends Principal, Serializable {
      
      // 安全主体所具有的权限
      Collection<? extends GrantedAuthority> getAuthorities();
      
      // 证明主体有效性的凭证
      Object getCredentials();
      
      // 认证请求的明细信息
      Object getDetails();
      
      // 主体的标识信息
      Object getPrincipal();
      
      // 是否认证通过
      boolean isAuthenticated();
      
      // 设置认证结果
      void setAuthenticated(boolean isAuthenticated) throws IllegalArgumentException;
      
  }
  ```

* AuthenticationProvider-认证的业务执⾏者：因为Authentication只代表了认证请求本身，所以具体执⾏认证的过程和逻辑需要由专⻔的组件来负责，这个组件就是 AuthenticationProvider。

  ```java
  public interface AuthenticationProvider {
      
      // 执⾏认证，返回认证结果
      Authentication authenticate(Authentication authentication) throws AuthenticationException;
      
      //判断是否⽀持当前的认证对象
      boolean supports(Class<?> authentication);
      
  }
  ```

  
