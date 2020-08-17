### Why Session?

Because HTTP is stateless protocol, If the server wants to records the state of the client, it must know who the client is.

The typical applications are Shopping cart, Login status, the server create a session for each client to identify, trace client.

Session is saved in server side, unique. It can be stored in memory, database, or file. In case of cluster, share session should be considered, so often they save session in memory, cache.

> After response, the TCP is closed. But the client can add `Connection:keep-alive` to request head, the TCP connection will be kept, client use the same TCP to do next request. This way to save the overhead of creating new TCP connection and save the bandwidth.

### How to identify the client?

Client must have an identifier: Cookie.
The first time the server get the request, it will create a session in server memory, and then create a Cookie for the session ID in response. In the next requests, as long as the Cookie is valid, request will carry it to server, so the server know whether the request has an exist session(Who they are). 

**What if the browser forbid Cookie?**
We can use URL rewrite technic, append something like `sid=***` to the requesting URL.

**How to identify the user?**

This is regarding the login status. After the client login successfully, we can save an user object into session(not Cookie), next time the request is carrying session ID, server will fetch corresponding session to the request, we can check the session whether there is an user object. If yes, the user has been login. We can also set the expire time of user object to achieve re-login for long time no activity.

We can also store a user into Cookie and set expire time:

```java
  Cookie cookie = new Cookie("user", "suntao");
  cookie.setMaxAge(7*24*60*60);     // 一星期有效
  response.addCookie(cookie);
  ```
  Obtain Cookie:
  ```java
    // 因为取得的是整个网页作用域的Cookie的值，所以得到的是个数组
  Cookie[] cookies = request.getCookies();

  for(int i = 0 ; i < cookies.length ; i++){
   String name = cookies[i].getName() ;
    String value = cookies[i].getValue() ;
   }    
```
> Set Cookie Alive time: c.setMaxAge.
> Alive time of Cookie: Default is -1.
> Session Cookie: When alive time is negative, Cookie will be store on browser.
> Long time Cookie: When alive time is positive, Cookie will be store on file.


### HttpSession mechanism and servlet

- Get HttpSession object by `HttpServletRequest.getSession`, add key value information by `setAttribute()`, `invalidate()` to invalidate session.
- Browser has the same session ID if it's not closed.
- Session ID's Cookie key is: `JSESSIONID`
- Server don't know when the session will be invalid, so we can clean session regularly.
- Rewrite URL can be done by `response.encodeURL(url)`
