---
title: "Spring Security"
description: "Spring Security를 이용한 회원 가입 및 인증"
date: 2025-03-15 09:00:00 +0900
categories: [Spring]
tags: [spring, spring-security, authentication]
permalink: /spring/post-name-here-4/  # 기존 URL 유지 (검색 노출·utterances 댓글 연결)
---

## Spring Security를 이용한 회원 가입 및 인증

### Spring Security 동작과정
* Spring Security는 Filter 기반으로 동작한다, 따라서 Spring MVC와 별도로 관리 및 동작한다
* Filter는 Dispatcher Servlet으로 가기 전에 적용되므로 가장 먼저 URL 요청을 받지만, Interceptor는 Dispatcher와 Controller 사이에 위치한다.
  * Filter는 Client와 Resource 사이에 Request와 Response를 이용해 다양한 정보를 처리하는 목적이다
  * Web Context 개요 : Client -> Filter(여러개 존재 가능) -> DispatcherServlet -> Interceptor -> Controller

### Filter Chain
* Spring Security는 Filter Chain 이라는 다양한 기능을 가진 필터를 10개 이상 기본으로 제공한다.
* 다음은 Filter Chain의 대략적인 동작과정이다.
* ![security1](https://github.com/TwoEther/ShoppingMall_Project/assets/101616106/02277064-0205-4f06-8281-693fbdb1201d)
* 출처 : https://velog.io/@choidongkuen/Spring-Security-Spring-Security-Filter-Chain-%EC%97%90-%EB%8C%80%ED%95%B4

### Spring Security 인증 처리 과정
![security2](https://github.com/TwoEther/ShoppingMall_Project/assets/101616106/b580b2e0-1585-46fd-b28c-927792c7293b)
1. 이용자가 폼에 아이디, 비밀번호를 입력하면 AuthenticationFilter가 넘어온 정보에 대해 유효성 검사를 실시
2. 유효성 검사 후 실제 구현체인 UsernamePasswordAuthenticationToken을 만들어 넘겨줌
3. 인증용 객체인 UsernamePasswordAuthenticationToken을 AuthenticationManager 에게 전달
4. UsernamePasswordAuthenticationToken을 AuthenticationProvider에게 전달
5. 사용자 아이디를 UserDetailsService로 보내고 UserDetailsService는 사용자 아이디로 찾은 객체를 UserDetails 객체로 만들어 AuthenticationProvider에게 전달
6. DB에 있는 사용자의 정보를 가져와 UserDetails와 비교에 실제 인증 처리 시작
7. 인증이 완료되면 SecurityContextHolder에 Authentication을 저장
8. 인증 여부에 따라 성공 시 AuthenticationSuccessHandler, 실패 시 AuthenticationFailureHandler 실행

### Spring Security 설정
```java
package org.project.shop.config;

import org.project.shop.auth.CustomAuthenticationFailureHandler;
import org.project.shop.domain.Role;
import org.project.shop.service.PrincipalDetailService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.authentication.builders.AuthenticationManagerBuilder;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.csrf.HttpSessionCsrfTokenRepository;
import org.springframework.security.web.util.matcher.AntPathRequestMatcher;

@Configuration
@EnableWebSecurity
public class SecurityConfig {

    private final CustomAuthenticationFailureHandler customAuthenticationFailureHandler;

    public SecurityConfig(CustomAuthenticationFailureHandler customAuthenticationFailureHandler) {
        this.customAuthenticationFailureHandler = customAuthenticationFailureHandler;
    }

    @Bean
    SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests((authorizeHttpRequests) -> authorizeHttpRequests
                        .requestMatchers(new AntPathRequestMatcher("/admin")).hasAuthority(Role.ROLE_ADMIN.toString())
                        .requestMatchers(new AntPathRequestMatcher("/admin/**")).hasAuthority(Role.ROLE_ADMIN.toString())
                        .requestMatchers(new AntPathRequestMatcher("/item/edit")).hasAuthority(Role.ROLE_ADMIN.toString())
                        .anyRequest().permitAll())
                // 로그인 처리
                .formLogin((formLogin) -> formLogin
                        // 로그인 페이지
                        .loginPage("/member/LoginForm")
                        // 로그인이 성공했을 경우 url
                        .loginProcessingUrl("/member/login")
                        .defaultSuccessUrl("/")
                        // 로그인 실패 핸들러
                        .failureHandler(customAuthenticationFailureHandler)
                        .usernameParameter("userId")
                        .passwordParameter("password"))
                .logout((logout) -> logout
                        .logoutRequestMatcher(new AntPathRequestMatcher("/member/logout"))
                        .logoutSuccessUrl("/")
                        .invalidateHttpSession(true))
                .csrf(Customizer.withDefaults());
        return http.build();
    }
    
    // 해시 암호화
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }

    @Autowired
    private PrincipalDetailService principalDetailService;


    @Bean
    AuthenticationManager authenticationManager(AuthenticationConfiguration authenticationConfiguration) throws Exception {
        return authenticationConfiguration.getAuthenticationManager();
    }

    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.userDetailsService(principalDetailService).passwordEncoder(passwordEncoder());
    }
}
```

### 로그인 한 사용자 가져오기

#### 💎 Bean에서 직접 사용자 정보 가져오기
전역에 선언된 SecurityContextHolder 에서 가져오는 방법인다.
```java
private static String getMemberByBean() {
    Object principal = SecurityContextHolder.getContext().getAuthentication().getPrincipal();
    Member member = (Member)principal;
    return member.getUserId();
}
```

#### 💎 @AuthenticationPrincipal 어노테이션 선언
전역에 선언된 SecurityContextHolder 에서 가져오는 방법이다.
```java
// 배송지 설정 함수
@GetMapping(value = "/address")
public String setDeliveryMember(@AuthenticationPrincipal PrincipalDetails principalDetails,
                                Model model) throws IOException {
    // UserDetailsService의 구현체인 principalDetails 객체 정보 반환
    // 객체 정보에서 현재 로그인 한 유저 접근 가능
    String username = principalDetails.getUsername();
    Member findMember = memberServiceImpl.findByUserId(username);
    .
    .
    .
}
```

### 로그아웃 구현 함수
```java
@GetMapping("/logout")
  public String logout(HttpServletRequest request, HttpServletResponse response) {
      new SecurityContextLogoutHandler().logout(request, response, SecurityContextHolder.getContext().getAuthentication());
      return "redirect:/";
  }
```
