## Title 
OTP Verification: Response Manipulation Using Burp Suite


## Vulnerability Type
OTP Verification – Response Manipulation / Client-Side Trust Issue


## Target Application
Taskbear Web Application


## Vulnerable Endpoint
`POST /api/v1/uwa/auth/otp/verify`


## Description

During security testing, an OTP verification request was intercepted using Burp Suite.

The application initially returned a `401 Unauthorized` response with the message:

`OTP invalid or expired`

The response was then modified in Burp Suite to display a `200 OK` status code and a successful verification message.

This demonstrates that HTTP responses can be manipulated on the client side. However, changing the displayed response does not prove that the backend has accepted the invalid OTP.

Further validation is required to determine whether the application incorrectly trusts client-side verification results.


## Steps to Reproduce
1).Open the application and start OTP verification using an authorized test account.

2).Launch Burp Suite and configure the browser proxy.

3).Intercept the OTP verification POST request.

4).Identify the endpoint: /api/v1/uwa/auth/otp/verify.

5).Forward the request, examine the server response, and modify the response in the testing environment.

6).Verify whether the application actually grants access or only displays a success message.

## Original Server Response

**HTTP Status:** `401 Unauthorized`

HTTP/2 401 Unauthorized
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://taskbear.com.au
Alt-Svc: h3=":443"; ma=2592000
Content-Type: application/json; charset=utf-8
Date: Fri, 30 Oct 2026 12:20:33 GMT
Etag: W/"35-ztW9fC5WL9mEe1dXWml1pD/iaR0"
Vary: Origin, Accept-Encoding
X-Powered-By: Express
X-Ratelimit-Limit: 10
X-Ratelimit-Remaining: 9
X-Ratelimit-Reset: 1
Content-Length: 61

{"statusCode":401,"message":"OTP invalid or expired"}


## Modified Response

**HTTP Status:** `200 OK`

HTTP/2 200 OK
Access-Control-Allow-Credentials: true
Access-Control-Allow-Origin: https://taskbear.com.au
Alt-Svc: h3=":443"; ma=2592000
Content-Type: application/json; charset=utf-8
Date: Fri, 30 Oct 2026 12:20:33 GMT
Etag: W/"35-ztW9fC5WL9mEe1dXWml1pD/iaR0"
Vary: Origin, Accept-Encoding
X-Powered-By: Express
X-Ratelimit-Limit: 10
X-Ratelimit-Remaining: 9
X-Ratelimit-Reset: 1
Content-Length: 53

{"statusCode":200,"message":"Successful"}


## POC

Screenshots documenting the original and modified responses:

<img width="1482" height="736" alt="image" src="https://github.com/user-attachments/assets/0df9f9a0-1930-4466-9217-78402f9de027" />
<img width="1486" height="631" alt="image" src="https://github.com/user-attachments/assets/a6ef354b-6cad-499a-b07f-a4d97118fd5e" />
<img width="1492" height="612" alt="image" src="https://github.com/user-attachments/assets/493e5b73-5468-497f-9b8c-25f956dda4e6" />

## Impact

If an application trusts client-side response data instead of enforcing OTP verification on the server, an attacker may potentially bypass authentication controls.

Potential consequences include:

- Unauthorized account access.
- Bypass of OTP-based authentication.
- Weakening of multi-factor authentication.

These impacts are conditional and require confirmation through authorized testing.


## Remediation

1. Always validate OTPs on the server side.
2. Never trust client-side success messages or HTTP status codes as proof of authentication.
3. Generate short-lived, single-use OTPs.
4. Implement OTP expiry and attempt limits.
5. Invalidate OTPs after successful verification.
6. Ensure protected endpoints independently check the authenticated session and verification state.

