---
title: "Docker部署Keycloak"
date: 2024-11-30
---

## 学习资料

GitHub：https://github.com/keycloak/keycloak

## Keycloak 的功能和概念

Keycloak 是适用于 Web 应用和 RESTful Web 服务的单点登录解决方案。Keycloak 的目标是简化安全性，以便应用程序开发人员可以轻松保护他们在组织中部署的应用程序和服务。开发人员通常必须自己编写的安全功能现已开箱即用，并且可以轻松根据组织的个性化需求进行定制。Keycloak 为登录、注册、管理和帐户管理提供了可自定义的用户界面。您还可以将 Keycloak 用作集成平台，将其挂接到现有的 LDAP 和 Active Directory 服务器中。您还可以将身份验证委托给第三方身份提供商，例如 Facebook 和 Google。

## 特征

Keycloak 提供以下功能：

- 浏览器应用程序的单点登录和单点注销。
- OpenID Connect 支持。
- OAuth 2.0 支持。
- SAML 支持。
- 身份经纪 - 使用外部 OpenID Connect 或 SAML 身份提供商进行身份验证。
- 社交登录 - 支持使用 Google、GitHub、Facebook、Twitter 和其他社交网络登录。
- 用户联合 - 从 LDAP 和 Active Directory 服务器同步用户。
- Kerberos 桥接器 - 自动验证登录到 Kerberos 服务器的用户。
- 管理控制台用于集中管理用户、角色、角色映射、客户端和配置。
- 帐户控制台允许用户集中管理他们的帐户。
- 主题支持——自定义所有面向用户的页面，以便与您的应用程序和品牌相结合。
- 双因素身份验证 - 通过 Google Authenticator 或 FreeOTP 支持 TOTP/HOTP。
- 登录流程-可选的用户自我注册、恢复密码、验证电子邮件、要求更新密码等。
- 会话管理-管理员和用户自己可以查看和管理用户会话。
- 令牌映射器 - 将用户属性、角色等按照您想要的方式映射到令牌和语句中。
- 每个领域、应用程序和用户的未撤销政策。
- CORS 支持-客户端适配器内置对 CORS 的支持。
- 服务提供商接口 (SPI) - 多个 SPI 可用于自定义服务器的各个方面。身份验证流程、用户联合提供商、协议映射器等等。
- 支持任何具有 OpenID Connect 依赖方库或 SAML 2.0 服务提供商库的平台/语言。

## 什么是Keycloak

开源身份与访问管理

以最少的工作量为应用程序添加身份验证和保护服务。无需处理存储用户或验证用户。

无需处理存储用户或验证用户。

Keycloak 提供用户联合、强身份验证、用户管理、精细授权等。

## Docker部署

```bash
docker run -d -p 8180:8080 -e KC_BOOTSTRAP_ADMIN_USERNAME=admin -e KC_BOOTSTRAP_ADMIN_PASSWORD=admin -e KC_DB=mysql -e KC_DB_URL=jdbc:mysql://47.93.101.254:5505/test_keycloak -e KC_DB_USERNAME=root -e KC_DB_PASSWORD=Fyl666666@@ quay.io/keycloak/keycloak:26.0.6 start-dev
```

