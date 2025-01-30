# Authentication
![ProptechOS logo](../../images/ProptechOS-logotype-ex.png)  
Authentication in ProptechOS uses OAuth 2.0 protocol.
It can be separated into two categories:
* Interactive authentication (for applications accessing the API on behalf of user, like web apps and UIs)
* Application (daemon) authentication, for applications working without user interaction.

In both cases, the final goal is to obtain an Access Token, which is used in the HTTP header for every call.

Authentication is performed in Microsoft Identity Platform (former Active Directory (AD)), so the user needs to have an account in Idun AD. In case of application authentication, the application needs to have its own ID in Idun AD. After the user and/or application has authenticated itself, it obtains a token in JWT format, which is then put into the Authorization header for subsequent calls to the ProptechOS REST API.  
It has to be prepended by Bearer clause:
```
--header 'Authorization: Bearer XXXXXX_TOKEN_BODY_XXXXXXXXXX'  
```

## 1. Interactive authentication uses OAuth Implicit Grant flow.

For Web UI application that users are interacting with, there is MSAL for Javascript Library provided by Microsoft, that allows to perform authorization of the user in the frame of a UI. In this case, the application (usually, web page) is acting on behalf of the user that was authenticated.
The library exists for several JS frameworks as well as framework-agnostic version.
Details can be found here:
https://github.com/AzureAD/microsoft-authentication-library-for-js

Depending on the type of usage, the refreshing of the token is handled by MSAL library.

The application that wants to use the ProptechOS API with Implicit flow authentication needs to have its own identity in Idun's Active Directory, and the correct Redirect URI registered.

An example of how to use it can be found in the **[authentication/examples folder](../examples)**.


```
// Line breaks for legibility only
GET  https://proptechos.b2clogin.com/0182be13-23bf-45ec-a6de-054f50a9a0c0/b2c_1a_signin/oauth2/v2.0/authorize?
client_id={ client id }
&response_type=id_token
&redirect_uri=http%3A%2F%2myApp.com%2Fmyredirectendpoint%2F
&scope=Api.Use
&response_mode=fragment
&state=12345
&nonce=678910
```

important to note:
* tenant: this URL is for the Idun ProptechOS tenant (0182be13-23bf-45ec-a6de-054f50a9a0c0)
* client_id: ID of the application, obtained after Application registration in ProptechOS **Replace with your client id**
* redirect_uri: upon Application registration for the implicit auth flow you will state your redirect uri. **Replace myapp.com/myredirectendpoint with your redirect endpoint**

Read more: [Microsoft Docs - MSAL Authentication Flows Authorization Code](https://docs.microsoft.com/en-us/azure/active-directory/develop/msal-authentication-flows#authorization-code)

## 2. Application Authentication.

In case of this type of authorization, a 'Client Credentials' OAuth flow is used. The application has its own id _and password_ ("secret"), and uses it to obtain a token.

The application that wants to use Idun API with Application authentication needs to have its own identity in Idun's Active Directory. As opposed to Implicit auth (above) the application will _not need to register a Redirect URI_, but _need to ask for a Client Secret to be generated_. This is done by Property Owner, Organization or Idun Admin once for each application. After the creation of identity, the ID and Secret of the application is obtained.

The most straightforward and universal (language-independent) way for application to authenticate itself is directly using the HTTP POST method on Microsoft authentication endpoint.

```
//Line breaks for clarity
POST https://login.microsoftonline.com/0182be13-23bf-45ec-a6de-054f50a9a0c0/oauth2/v2.0/token?
client_id={ client ID }
&client_secret={ client secret }
&scope=https://proptechos.onmicrosoft.com/multi/api/.default
&grant_type=client_credentials
```

important to note:
* tenant: this URL is for the Idun ProptechOS tenant (0182be13-23bf-45ec-a6de-054f50a9a0c0)
* client_id: ID of the application, obtained after Application registration in ProptechOS. **Replce with your client id**
* client_secret: also generated after Application registration by Idun **Replace with your client secret**
* scope: Set the ProptechOS API as scope.
* grant_type: must be 'client_credentials' (indicates which auth flow).


Another way is to use MSAL Library provided by Microsoft. An example of how to use it can be found in the **[authentication/examples folder](../examples)**.

The token provided has an expiration time. Usually it is one hour, and it is up to an application to track this timeout and/or expiration errors, and to retrieve new access tokens in time.

Current version is  '0.5.0-preview'   
Details how to use the lib in the project can be found here:
https://github.com/AzureAD/microsoft-authentication-library-for-java

## Register an application in ProptechOS
Registering the applications can be done by a Property Owner or Organization Admin in the Swagger API or UI. After an application is registered its ID and secret key are possible to obtain in either Swagger API or UI. If assistance is needed a request to register an application should be sent to onboarding@proptechos.com.


## Resources

For more information about Microsoft Identity Platform, OAuth2 and MSAL Libraries for different languages, it is recommended to
visit Microsoft official documentation

```text
https://docs.microsoft.com/en-us/azure/active-directory/develop/authentication-scenarios
```
## Migration from old Authentication method.

In previous version, Idun authentication was done by putting predefined Authorization code into Authorization header.
In a new version, the procedure is basically the same, the only difference is that the token is not statically defined,
but obtained during the authentication process described above.
Another difference is that the token is preceded by 'Bearer ' keyword in Authorization header.
Also, in case of 'Daemon application', the token refreshment must be coded and daemon must take care that the token is not expired.
Additionally, proper error handling in case the token validation failed, must be performed.

In case of MSAL libraries for Javascript (Angular or other frameworks), the token refreshment is performed by the library itself.
