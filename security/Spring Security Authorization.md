# Spring Security Authorization

## 授权架构

### Authorities

Authentication接口：

```java
Collection<? extends GrantedAuthority> getAuthorities();
```

GrantedAuthority:

```java
String getAuthority();
```

GrantedAuthority的实现SimpleGrantedAuthority:



### 预调用处理



`Spring Security` 提供`拦截器`来控制安全对象的访问权限。比如方法调用，web请求等。

`AccessDecisionManager`来决定。



#### AccessDecisionManager



```java
void decide(Authentication authentication, Object secureObject,
	Collection<ConfigAttribute> attrs) throws AccessDeniedException;

boolean supports(ConfigAttribute attribute);

boolean supports(Class clazz);
```

`supports（ConfigAttribute）`方法在启动的时候就被`AbstractSecurityInterceptor`调用，用来决定`AccessDecisionManager`是否处理传递过来配置项。

`supports(Class)`方法被security拦截器的实现类调用，用来确保配置过的`AccessDecisionManager`能够支持拦截器呈现的对象类型。



#### 基于投票的AccessDecisionManager实现

![v7H9h.png](https://s1.ax2x.com/2018/02/09/v7H9h.png)



`AccessDecisionVoter`接口：

```java
boolean supports(ConfigAttribute attribute);

boolean supports(Class<?> clazz);

int vote(Authentication authentication, S object,
			Collection<ConfigAttribute> attributes);
```

具体实现中的`vote`方法返回的`int`值有下面三种：

* `ACCESS_GRANTED`: 1，通过
* `ACCESS_ABSTAIN`: 0，弃权
* `ACCESS_DENIED`: -1，否决

`AccessDecisionManager` 的三种实现：

* `ConsensusBased` : 

  ```java
  private boolean allowIfEqualGrantedDeniedDecisions = true;

  默认当否决票数==赞成票数时，允许通过。
  ```

* `AffirmativeBased` : 有一票通过则通过

* `UnanimousBased` : 有一票否决则直接抛出`AccessDeniedException`

`AccessDecisionVoter`的两种实现：

* `RoleVoter`：如果`ConfigAttribute`中以`Role_`前缀开头，并且有一票否决则直接抛出`AccessDeniedException`返回的一个代表`Authority`的`String`字符串与`ConfigAttributes`中的一个或多个匹配，则通过，否则`RoleVoter`将否决授权。如果`ConfigAttribute`不是以`Role_`前缀开头，则弃权。
* `AuthenticatedVoter`：



### 调用后处理



[![after-invocation.png](https://i.loli.net/2018/02/09/5a7d407a1ce24.png)](https://i.loli.net/2018/02/09/5a7d407a1ce24.png)



`AfterInvocationManager`有一个简单的实现：`AfterInvocationProviderManager`。

每个`AfterInvocationProvider`允许修改返回的Object对象或者抛出`AccessDeniedException`。



### 层次化角色



`RoleVoter`的一个扩展类：`RoleHierarchyVoter`,内部有个配置属性`roleHierarchy`：

```java
private RoleHierarchy roleHierarchy = null;
```

`RoleHierarchy`接口定义如下：

```java
public interface RoleHierarchy {

	/**
	 * Returns an array of all reachable authorities.
	 * <p>
	 * Reachable authorities are the directly assigned authorities plus all authorities
	 * that are (transitively) reachable from them in the role hierarchy.
	 * <p>
	 * Example:<br>
	 * Role hierarchy: ROLE_A &gt; ROLE_B and ROLE_B &gt; ROLE_C.<br>
	 * Directly assigned authority: ROLE_A.<br>
	 * Reachable authorities: ROLE_A, ROLE_B, ROLE_C.
	 *
	 * @param authorities - List of the directly assigned authorities.
	 * @return List of all reachable authorities given the assigned authorities.
	 */
	public Collection<? extends GrantedAuthority> getReachableGrantedAuthorities(
			Collection<? extends GrantedAuthority> authorities);

}
```

`xml`配置如下：

```xml
<bean id="roleVoter" class="org.springframework.security.access.vote.RoleHierarchyVoter">
	<constructor-arg ref="roleHierarchy" />
</bean>
<bean id="roleHierarchy"
		class="org.springframework.security.access.hierarchicalroles.RoleHierarchyImpl">
	<property name="hierarchy">
		<value>
			ROLE_ADMIN > ROLE_STAFF
			ROLE_STAFF > ROLE_USER
			ROLE_USER > ROLE_GUEST
		</value>
	</property>
</bean>
```