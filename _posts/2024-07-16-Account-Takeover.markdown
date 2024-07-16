---
layout: single
title:  "AppSec Travels: Part 3"
date:   2024-07-16 10:30:00 -0600
categories: posts
permalink: /AppSec-Travels-3-Account-Takeover/
author: Justin Larson
author_profile: true
classes: wide
---

# <span style="color:red">Account Takeover </span>

  
During a recent assessment, our team came upon a vulnerability that felt like finding a hidden door in a seemingly secure fortress. The discovery involved the password reset mechanism of an application, allowing us to reset any user's password with just their email address. This flaw circumvents authentication, giving unauthorized access to user accounts. Here’s how we uncovered it and what it means for application security.

## Token Caching in the Wild

Recently we were tasked with testing an API, a simple, straight-forward assessment with only a handful endpoints. We were supplied the source code in order to validate findings and offer better recommendations on fixes. As part of our approach we focus primarily on Authentication and Authorization issues. In our view these are some of the most prevalent failings of modern applications and can lead to the greatest amount of disruption to an application. During the assessment we were validating the forgot password mechanism. Generally the issue we find here is username enumeration, which was present here. There was also the ability to leverage a forgot password vulnerability into an account takeover which is what we want to discuss in this post. 

## Forgot Password 
The forgot password process is a straight forward process, give the application your email address and the server will send you a link in the email provided with a way to reset your password. The link will contain a token or some value that is associated with reseting your password. The below image shows the link from the email which contains the password reset token and the user's email address. 


![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/3/appsec-travels-3-bac-1.png' }})
_Email received after a password reset request for user.test@rdpt.io_

When the user clicks the link it will take them to a page to enter a new password and submit the form. Behind the scenes the PUT request for resetting the password takes the forgot password token, the new password, and user's email address from the link. 


![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/3/appsec-travels-3-bac-2.png' }})
_Password reset request_

Here is where the vulnerability enters the process. If you notice in the above screenshot the email address has changed from `user.test@rdpt.io` to `admin.test@rdpt.io` but the password reset token is the same. The request was accepted by the server which returned a 204 response. After submitting the password reset for the admin user we then validated that it worked and the below screenshot shows that indeed did. 

![Attack]({{ site.baseurl }}{{ '/assets/images/appsec-travels/3/appsec-travels-3-bac-3.png' }})
_Password validation test_

So what happened here. The code creating the password reset token was properly implemented. It created a new password reset token, added the user's email that was requesting the password reset, and set it in a Redis cache with a 24 hour time to live. 

```go
redisErr := redis.SetObjectWithTemplate(
		cache.ForgotPasswordTemplate,
		new(cache.ForgotPasswordKey).Init(redisUUID),
		user.Email,
		24*time.Hour)
```

The problem occurred when validating the password token. The code only validated that the password reset token existed in the Redis cache, it did not validate the email address supplied was correlated to the password reset token. The code below shows only the password reset token being validated. 


```go
if exists := redis.ObjectExistsWithTemplate(
		cache.ForgotPasswordTemplate,
		new(cache.ForgotPasswordKey).Init(*passwordReset.Token)); exists {

		if a, b, c, d, e := functions.VerifyPassword(passwordReset.NewPassword, 8); !a || !b || !c || !d || !e {
			logger.Info("Could not change password. New password does not meet minimum requirements")
			ctx.AbortWithStatusJSON(
				http.StatusBadRequest,
				gin.H{"error": "Could not change password. " +
					"New password does not meet minimum requirements: " +
					"1 uppercase letter, 1 lowercase letter, 1 number, 1 symbol, and a minimum length of 8."})
			return
		}

		user.Password = &passwordReset.NewPassword

```


This allowed any user's password to be reset. There were also multiple instances of username enumeration throughout the application so the likelihood of a successful account take over increased greatly. We would love to say that is the only time we have seen this but unfortunately it is not. We have seen it worse however, in one instance we were given a password reset token that didn't expire and could be used an unlimited amount of times but in order to reset the password you had to know the the users 8 digit id which was really easy to iterate through in order to reset **ALL** the users' passwords in just a few minutes. So this could have been worse. The fix we suggested was to validate that the email supplied in the forgot password was the same that was being reset. Our client had this fixed in no time. 