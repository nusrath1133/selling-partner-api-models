# Selling Partner API Authentication/Authorization Library
This library provides helper classes for use when signing HTTP requests for Amazon Selling Partner APIs. It is intended for use
with the Selling Partner API Client Libraries generated by [swagger codegen](https://swagger.io/tools/swagger-codegen/) 
using the RestSharp library. It can also be integrated into custom projects.

## LWAAuthorizationSigner
Obtains and signs a request with an access token from LWA (Login with Amazon) for the specified endpoint using the provided LWA credentials.

*Example*
```
using RestSharp;
using Amazon.SellingPartnerAPIAA;

string resource = "/my/api/path";
RestClient restClient = new RestClient("https://...");
IRestRequest restRequest = new RestRequest(resource, Method.GET);

// Seller APIs
LWAAuthorizationCredentials lwaAuthorizationCredentials = new LWAAuthorizationCredentials
{
    ClientId = "...",
    ClientSecret = "",
    RefreshToken = "",
    Endpoint = new Uri("...")
};

/* Sellerless APIs
The Selling Partner API scopes can be retrieved from the ScopeConstants class and used to specify a list of scopes of an LWAAuthorizationCredentials instance. */
LWAAuthorizationCredentials lwaAuthorizationCredentials = new LWAAuthorizationCredentials
{
    ClientId = "...",
    ClientSecret = "",
    Scopes = new List<string>() { ScopeConstants.ScopeNotificationsAPI, ScopeConstants.ScopeMigrationAPI }
    Endpoint = new Uri("...")
};

restRequest = new LWAAuthorizationSigner(lwaAuthorizationCredentials).Sign(restRequest);  
```
Note the IRestRequest reference is treated as **mutable** when signed.

## AWSSigV4Signer
Signs a request with [AWS Signature Version 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)
using the provided AWS developer account credentials. 

This implementation of the IAM Role-based Authentication, will work only as long as the initial STS Token is valid (typically for 3600 seconds) and until an instance is able to refresh the STS Token on its own, otherwise, the Token needs to be reinitialized via the AWSSigV4Signer.

*Example*
```
using RestSharp;
using Amazon.SellingPartnerAPIAA;

string resource = "/my/api/path";
RestClient restClient = new RestClient("https://...");
IRestRequest restRequest = new RestRequest(resource, Method.GET);

AWSAuthenticationCredentials awsAuthenticationCredentials = new AWSAuthenticationCredentials 
{
    AccessKeyId = "..."
    SecretKey = "..."
    Region = "..."
};

AWSAuthenticationCredentialsProvider awsAuthenticationCredentialsProvider = new AWSAuthenticationCredentialsProvider
{
    RoleArn = "...",
    RoleSessionName = "..."
};

restRequest = new AWSSigV4Signer(awsAuthenticationCredentials, awsAuthenticationCredentialsProvider)
    .Sign(restRequest, restClient.BaseUrl.Host);
```
Note the IRestRequest reference is treated as **mutable** when signed.

## Resources
This package features Mustache templates designed for use with [swagger codegen](https://swagger.io/tools/swagger-codegen/). 
When you build Selling Partner API Swagger models with these templates, they help generate a rich SDK with functionality to invoke Selling Partner APIs built in. The templates are located in *resources/swagger-codegen*.
 
## Building
This package is built as a .NET Standard Library via a Visual Studio Solution with implementation and test projects.  The Visual Studio Community Edition can be obtained for free from Microsoft and used to build the solution and generate a .dll assembly which can be imported into other projects.

## Dependencies
All dependencies can be installed via NuGet
- RestSharp - 105.1.0
- Newtonsoft.Json 12.0.3
- NETStandard.Library 2.0.3 (platform-specific implementation requirements are documented on the [Microsoft .NET Guide](https://docs.microsoft.com/en-us/dotnet/standard/net-standard))
- AWSSDK.Core 3.5.1.23
- AWSSDK.SecurityToken 3.5.1.5

## License
Swagger Codegen templates are subject to the [Swagger Codegen License](https://github.com/swagger-api/swagger-codegen#license).

All other work licensed as follows:

Copyright 2020 Amazon.com, Inc

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this library except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.