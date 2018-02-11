# Secure Object Implementations



## AOP Alliance Security Interceptor

`MethodSecurity`通过`MethodSecurityInterceptor`来控制。

拦截器使用一个`MethodSecurityMetadataSource`来获取应用在方法上的配置属性。



###  Explicit MethodSecurityInterceptor Configuration

配置一个`MethedSecurityInterceptor`：

```xml
<bean id="bankManagerSecurity" class=
	"org.springframework.security.access.intercept.aopalliance.MethodSecurityInterceptor">
<property name="authenticationManager" ref="authenticationManager"/>
<property name="accessDecisionManager" ref="accessDecisionManager"/>
<property name="afterInvocationManager" ref="afterInvocationManager"/>
<property name="securityMetadataSource">
	<sec:method-security-metadata-source>
	<sec:protect method="com.mycompany.BankManager.delete*" access="ROLE_SUPERVISOR"/>
	<sec:protect method="com.mycompany.BankManager.getBalance" access="ROLE_TELLER,ROLE_SUPERVISOR"/>
	</sec:method-security-metadata-source>
</property>
</bean>
```



## AspectJ (JoinPoint) Security Interceptor

`AspectJ`拦截器`AspectJSecurityInterceptor`，用于领域对象安全。

配置如下：

```xml
<bean id="bankManagerSecurity" class=
	"org.springframework.security.access.intercept.aspectj.AspectJMethodSecurityInterceptor">
<property name="authenticationManager" ref="authenticationManager"/>
<property name="accessDecisionManager" ref="accessDecisionManager"/>
<property name="afterInvocationManager" ref="afterInvocationManager"/>
<property name="securityMetadataSource">
	<sec:method-security-metadata-source>
	<sec:protect method="com.mycompany.BankManager.delete*" access="ROLE_SUPERVISOR"/>
	<sec:protect method="com.mycompany.BankManager.getBalance" access="ROLE_TELLER,ROLE_SUPERVISOR"/>
	</sec:method-security-metadata-source>
</property>
</bean>
```



## Expression-Based Access Control



### Common Built-In Expressions

`SecurityExpressionRoot`：提供了在`web`或者`method`安全通用表达式：

| Expression                                                   | Description                                                  |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| `hasRole([role])`                                            | Returns `true` if the current principal has the specified role. By default if the supplied role does not start with 'ROLE_' it will be added. This can be customized by modifying the `defaultRolePrefix` on `DefaultWebSecurityExpressionHandler`. |
| `hasAnyRole([role1,role2])`                                  | Returns `true` if the current principal has any of the supplied roles (given as a comma-separated list of strings). By default if the supplied role does not start with 'ROLE_' it will be added. This can be customized by modifying the `defaultRolePrefix` on `DefaultWebSecurityExpressionHandler`. |
| `hasAuthority([authority])`                                  | Returns `true` if the current principal has the specified authority. |
| `hasAnyAuthority([authority1,authority2])`                   | Returns `true` if the current principal has any of the supplied roles (given as a comma-separated list of strings) |
| `principal`                                                  | Allows direct access to the principal object representing the current user |
| `authentication`                                             | Allows direct access to the current `Authentication` object obtained from the `SecurityContext` |
| `permitAll`                                                  | Always evaluates to `true`                                   |
| `denyAll`                                                    | Always evaluates to `false`                                  |
| `isAnonymous()`                                              | Returns `true` if the current principal is an anonymous user |
| `isRememberMe()`                                             | Returns `true` if the current principal is a remember-me user |
| `isAuthenticated()`                                          | Returns `true` if the user is not anonymous                  |
| `isFullyAuthenticated()`                                     | Returns `true` if the user is not an anonymous or a remember-me user |
| `hasPermission(Object target, Object permission)`            | Returns `true` if the user has access to the provided target for the given permission. For example, `hasPermission(domainObject, 'read')` |
| `hasPermission(Object targetId, String targetType, Object permission)` | Returns `true` if the user has access to the provided target for the given permission. For example, `hasPermission(1, 'com.example.domain.Message', 'read')` |



