---
title: 8Weike API Doc

language_tabs:
  - http  

toc_footers:  
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to the 8Weike API!

Languages available in Shell, JavaScript and Swift! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

# Credential

## Login using cellphone number

```http
POST /api/login/phone HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> JSON response with error:

```json
{
  "error": {
    "statusCode": 401,
    "message": "User doesn't exist!"
  },
  "credential": null
}
```

> JSON response with no error:

```json
{
  "error": null,
  "credential": {
    "id": 2,
    "profileId": 1
  }    
}
```

This endpoint logs in the user with phone as credential.

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
phone | string | null | The cellphone number
password | string | null | User password

## Login using WeChat credential

```http
POST /api/login/wechat HTTP/1.1
Accept: application/json
Host: http://8weike.com 
```

This endpoint logs in a user with WeChat credential

<aside class="warning">
Not implemented yet
</aside>

## Login using Weibo credential

```http
POST /api/login/weibo HTTP/1.1
Accept: application/json
Host: http://8weike.com 
```

This endpoint logs in a user with Weibo credential

<aside class="warning">
Not implemented yet
</aside>
