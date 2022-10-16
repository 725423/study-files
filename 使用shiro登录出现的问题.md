# 使用shiro登录出现的问题

1. ```java
   Field userRealm in cn.zb.project.config.ShiroConfig required a bean of type 'cn.zb.project.realm.UserRealm' that could not be found.
    Action:
   
   Consider defining a bean of type 'cn.zb.project.realm.UserRealm' in your configuration.
       
     字段用户在 cn.zb.project.config.ShiroConfig 中需要一个类型为“cn.zb.project.realm.用户”的 Bealm，但找不到。
     考虑在配置中定义一个类型为“cn.zb.项目.领域.用户”的 Bean。  
   ```

   解决方案：配置bean，传入userRealm对象

   ```java
   @Bean
       public UserRealm userRealm(){
           UserRealm userRealm = new UserRealm();
           return userRealm;
       }
   ```

2. ```java
   org.apache.shiro.UnavailableSecurityManagerException: No SecurityManager accessible to the calling code, either bound to the org.apache.shiro.util.ThreadContext or as a vm static singleton.  This is an invalid application configuration.
       
       还未解决
   ```

3. 可能出现包依赖问题，查看是否出现依赖冲突：在pom.xml中使用ctrl+alt+shift+u查看

   