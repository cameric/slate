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

**Important Note:**

In production environment, all API request should be sent to:

`
https://oav0gkqdmk.execute-api.us-east-1.amazonaws.com/prod
`

In dev/test, API requests should be sent to:

`
https://oav0gkqdmk.execute-api.us-east-1.amazonaws.com/test
`

In the future, once we acquire an SSL certificate, we will setup a 
custom domain name so that we could use a more symbolic api server name.

Note that the `host:` for each endpoint below signifies which server
handles the request. However, **DO NOT send request directly to server!**
Always use the API gateway above.

# Credential

## Login using cellphone number

```http
POST /login/phone HTTP/1.1
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

### Error codes

- 401 if user provides invalid credential
- 400 for all other errors

## Login using WeChat credential

```http
POST /login/wechat HTTP/1.1
Accept: application/json
Host: http://8weike.com 
```

This endpoint logs in a user with WeChat credential

<aside class="warning">
Not implemented yet
</aside>

## Login using Weibo credential

```http
POST /login/weibo HTTP/1.1
Accept: application/json
Host: http://8weike.com 
```

This endpoint logs in a user with Weibo credential

<aside class="warning">
Not implemented yet
</aside>

## Check login status

```http
GET /login HTTP/1.1
Host: http://8weike.com    
```

> JSON response if logged in:

```json
{  
  "statusCode": 200,    
  "credential": {
    "id": 1
  }
}
```

> JSON response if not logged in:

```json
{  
  "statusCode": 200,    
  "credential": null
}
```

Check if the user has logged in and retrieve credential ID if so

## Signup (Web only)

```http
POST /signup/phone/web HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "User already exists!"
  }  
}
```

Web-only signup endpoint with cellphone. It will send a TFA code to phone.
        
<aside class="warning">
Note: This endpoint involves string-based Captcha check so it's not suitable for mobile environment.
</aside>

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
phone | string | null | The cellphone number for the new user
password | string | null | User password
captcha | string | null | The user-input of the captcha image
hash | string | null | The original bcrypted hash that corresponds to the actual captcha value

### Error codes

- 400 if one of the following scenarios occurs:
    - Incorrect captcha
    - Credential already exists in database
- 500 if one of the following scenarios occurs:
    - Error occurred in the process of sending TFA code
    - Error occurred during session saving
    
## Signup (Mobile only)

```http
POST /signup/phone/mobile HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "User already exists!"
  }  
}
```

Mobile-only signup endpoint with cellphone. It will send a TFA code to phone.
        
<aside class="warning">
Note: This endpoint does not go through Captcha check so it's not suitable for web.
</aside>

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
phone | string | null | The cellphone number for the new user
password | string | null | User password

### Error codes

- 400 if Credential already exists in database                    
- 500 if one of the following scenarios occurs:
    - Error occurred in the process of sending TFA code
    - Error occurred during session saving
    
## Signup verify

```http
POST /signup/verify HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Code is incorrect!"
  },  
  "credential": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "credential": {
    "id": 1
  }
}
```

Verify the TFA code and save user credential into database       

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
code | string | null | The user-input TFA code

### Error codes

- 400 if one of the following scenarios occurs:
    - User is not pending signup
    - TFA code is incorrect    
- 500 if other error occurred
    
# Profile

## Create profile

```http
POST /profiles HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  },  
  "profile": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "profile": {
    "profileId": 1
  }
}
```
       
Create a new profile and associate the login credential with it
       
<aside class="success">
This endpoint requires that the user has logged in
</aside>

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
nickname | string | null | the nickname for the profile

### Error codes

400 if error occurred in the middle of creating the profile

## Retrieve the profile of the currently logged in user

```http
GET /profiles/me HTTP/1.1
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  },  
  "profile": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "profile": {
    "nickname": "Cam",
    "description": "CEO of Cameric",
    "avatar": "FGHSDF8R82DFSFHDS",
    "[other fields]": "..."
  }
}
```

Retrieve the full representation of the profile of currently logged in user

<aside class="success">
This endpoint requires that the user has logged in
</aside>

<aside class="warning">
The response is NOT complete! Will fill in more information as more features are built
</aside>

### Error codes

400 if error occurred in the middle of retrieving the profile

## Retrieve the profile of a user

```http
GET /profiles/:profile_id HTTP/1.1
Host: http://8weike.com
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  },  
  "profile": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "profile": {
    "nickname": "Tony",
    "description": "Engineer of Cameric",
    "avatar": "efhuwehu23ur32iu",
    "[other fields]": "..."
  }
}
```

Retrieve the full representation of a profile of user passed in as the url param

<aside class="warning">
The response is NOT complete! Will fill in more information as more features are built
</aside>

### Error codes

400 if error occurred in the middle of retrieving the profile

# Media

## Media object

```json
{
  "name": "NatGeo03",
  "original": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.jpg",
  "medium": null,
  "thumbnail": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.thumbnail.jpg"
}
```

Currently media doesn't have any open endpoint yet, but here are some general information about how to process the
media object correctly.

A media object contains a name, which is the original name of the uploaded file without extension, and the CDN urls to
three different versions of the image with various sizes (`ext` stands for the orignal extension, e.g. `jpg`):

name | Extension | Description
---- | --------- | -----------
original | .ext | the original image size
medium | .medium.ext | image resized to height of 400px and reduced quality to 90%
thumbnail | .thumbnail.ext | image resized to height of 64px and reduced quality to 80%

Note that there is possibility that either/both `medium` and `thumbnail` are null. There are two possible reasons for this scenario:

- the original image size is smaller than or equal to the processed image size (in which case the original image could be used directly)
- the image has not been finished processing yet

Given that the current traffic is relatively low, the second scenario shouldn't happen often. However,
there is one suggestion on the second scenario: the UI might need to have a "refresh" (scroll to refresh on mobile) feature
so that this endpoint will be hit again with the updated URL and probably at that time the image has finished processing.

# Post

## Create a new post

```http
POST /posts HTTP/1.1
Host: http://8weike.com    
Content-Type: multipart/form-data
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  },  
  "id": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "id": 3
}
```

Create a new post with corresponding media resources. Note that each media resource cannot exceed 5MB
and only up to 10 media files could be uploaded for each post.

The Id of the new post will be returned as response in order to fetch the post data later.

<aside class="success">
This endpoint requires that the user has logged in
</aside>

<aside class="warning">
The request **MUST BE** sent with by standard file upload, which will include a header of `Content-Type: multipart/form-data`. Otherwise the post will not
be created!
</aside>

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
title | string | null | the title of the post (required!)
description | string | null | the description of the post content
media | array | [] | an array of media data (each media resource cannot exceed 5MB)

### Error codes

400 if error occurred in the middle of creating the profile

## Retrieve posts for a given profile

```http
GET /profiles/:profile_id/posts HTTP/1.1
Host: http://8weike.com
```

> JSON response if error:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  }  
}
```

> JSON response if no error:

```json
{
  "error": null,
  "has_more": true,
  "posts": [
    {
      "title": "Photography",
      "description": "this is fucking beautiful",
      "created_at": "2016-11-16 23:59:59",
      "media": [
        {
          "name": "NatGeo03",
          "original": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.jpg",
          "medium": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.medium.jpg",
          "thumbnail": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.thumbnail.jpg"
        },
        {
          "name": "NatGeo04",
          "original": "https://8weike-media.s3.amazonaws.com/1/4/ecb3fbb68e118cdf7a1f05c7424da03d2b76dd5f-NatGeo04.jpg",
          "medium": null,
          "thumbnail": "https://8weike-media.s3.amazonaws.com/1/4/ecb3fbb68e118cdf7a1f05c7424da03d2b76dd5f-NatGeo04.thumbnail.jpg"
        }      
      ]
    }
  ]  
}
```

Retrieve a subset of posts for a given user. Note that a page number and a limit need to be provided
for pagination. If not provided, then start from beginning. By default, the post will be
ordered by created date. A `sort` parameter could be specified to sort by other fields.

Note that the response of this request will contain a boolean value `has_more`, indicating whether 
there are more posts that could be retrieved in the next page.

### Url Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
page | integer | null | the current page number
limit | integer | 10 | the number of posts for this request
sort | string | null | the field to be sorted by

### Example request

`
GET /profiles/1/posts?page=1&limit=10
`

## Retrieve a post

```http
GET /posts/:post_id HTTP/1.1
Host: http://8weike.com
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400,
    "message": "Error occurred!"
  },  
  "post": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "post": {
    "title": "Photography",
    "description": "this is fucking beautiful",
    "created_at": "2016-11-16 23:59:59",
    "media": [
      {
        "name": "NatGeo03",
        "original": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.jpg",
        "medium": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.medium.jpg",
        "thumbnail": "https://8weike-media.s3.amazonaws.com/1/4/0e9f75298af881b5c570d5c6ecbf3ad22da63fc0-NatGeo03.thumbnail.jpg"
      },
      {
        "name": "NatGeo04",
        "original": "https://8weike-media.s3.amazonaws.com/1/4/ecb3fbb68e118cdf7a1f05c7424da03d2b76dd5f-NatGeo04.jpg",
        "medium": null,
        "thumbnail": "https://8weike-media.s3.amazonaws.com/1/4/ecb3fbb68e118cdf7a1f05c7424da03d2b76dd5f-NatGeo04.thumbnail.jpg"
      }      
    ]
  }
}
```

Retrieve the content of a post and the media metadata that associates with this post

No url parameter needed except the post id.

### Error codes

400 if error occurred in the middle of creating the profile


# Locale

## Get locale code

```http
GET /locale HTTP/1.1
Host: http://8weike.com    
```

> No response for this endpoint, only status code

Get the current locale code if stored in the session. Otherwise the default locale code (`zh-CN`) is returned.

## Set locale code

```http
POST /locale HTTP/1.1
Accept: application/json
Host: http://8weike.com    
```

> No response for this endpoint, only status code

Save the locale into session

### Query Parameters

Parameter | Type | Default | Description
--------- | ---- | ------- | -----------
locale | string | null | the new locale code

# Utility

## Get Captcha image (Web only)

```http
POST /captcha HTTP/1.1
Host: http://8weike.com    
```

> JSON response if error occurred:

```json
{
  "error": {
    "statusCode": 400    
  },  
  "captcha": null
}
```

> JSON response if no error:

```json
{
  "error": null,
  "captcha": {
    "hash": "RUWREWHNBFSD9784324AER82IUQY23FAS4323RASFAEWD",
    "picture": [1, 3, 10, 25, ... , 20, 14]
  }
}
```

Get a captcha image data and hash

<aside class="warning">
This endpoint is not suitable for mobile since string-based captcha is not mobile-friendly
</aside>

### Error codes

400 if error occurred in the middle of retrieving captcha data

## Get global info

```http
GET /global_info HTTP/1.1
Host: http://8weike.com    
```

> JSON response:

```json
{
  "global_info": {
    "company": "Cameric",
    "version": "v1"
  }
}
```

Get server-side information about the company and product

<aside class="success">
Note: this endpoint can be used to test the status of API server
</aside>
