---
title: "Spring Security自定义安全过滤器"
date: 2024-12-01
---

## 介绍

自定义Spring Security过滤器。

## 创建自定义过滤器

创建Java类，继承OncePerRequestFilter类，重写doFilterInternal

```java
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import org.apache.commons.lang3.StringUtils;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import java.io.IOException;
import java.util.Objects;

/**
 * 登录图片验证码验证过滤器
 */
@Component
public class LoginImageVerificationCodeVerificationFilter extends OncePerRequestFilter {

    @Autowired
    private StringRedisTemplate stringRedisTemplate;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        if (request.getRequestURI().equals("/ajax/login")) {
            String imageVerificationCodeKey = request.getParameter("imageVerificationCodeKey");
            if (Objects.nonNull(imageVerificationCodeKey)) {
                String imageVerificationCode = request.getParameter("imageVerificationCode");

                String redisImageVerificationCode = stringRedisTemplate.opsForValue().get(imageVerificationCodeKey);

                if (StringUtils.equals(imageVerificationCode, redisImageVerificationCode)) {
                    filterChain.doFilter(request, response);
                    return;
                }
            }
            throw new AccessDeniedException("Access denied");
        }else{
            filterChain.doFilter(request, response);
        }
    }
}
```

## 注册过滤器

在Spring Security配置类中添加下面的bean。

```java
@Bean
    public FilterRegistrationBean<LoginImageVerificationCodeVerificationFilter> tenantFilterRegistration(LoginImageVerificationCodeVerificationFilter filter) {
        FilterRegistrationBean<LoginImageVerificationCodeVerificationFilter> registration = new FilterRegistrationBean<>(filter);
        registration.setEnabled(false);
        return registration;
    }
```

## 配置过滤器

在Spring Security配置类配置过滤器，.addFilterBefore(loginImageVerificationCodeVerificationFilter, DisableEncodeUrlFilter.class)

```java
@Autowired
private LoginImageVerificationCodeVerificationFilter loginImageVerificationCodeVerificationFilter;

@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
            .requestCache(cache -> cache.requestCache(new NullRequestCache()))//禁用请求缓存
            .addFilterBefore(loginImageVerificationCodeVerificationFilter, DisableEncodeUrlFilter.class)
            .csrf(AbstractHttpConfigurer::disable)
            .httpBasic(AbstractHttpConfigurer::disable)
            .formLogin(Customizer.withDefaults())
            .authorizeHttpRequests(authorize -> authorize
                    .dispatcherTypeMatchers(FORWARD, ERROR).permitAll()
                    .requestMatchers("/imageVerificationCode","/ajax/login").permitAll()
                    .anyRequest().authenticated()
            );

    return http.build();
}
```