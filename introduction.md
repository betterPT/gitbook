---
description: An introduction to the request <-> response flow and auth process.
---

# Introduction

## Request-Response Flow

![Request-Response Diagram](.gitbook/assets/mermaid-diagram-20200406233222.png)



## BetterPT Auth

BetterPT uses [Auth0](https://www.auth0.com) to manage and generate [JSON Web Tokens](https://jwt.io) for its partner APIs. **The credentials you need to get started will be sent via encrypted email - please reach out to us for access.**

#### Auth0 Quickstart

**1. Request the token**

There are many Auth0 compatible libraries listed [here.](https://auth0.com/docs/libraries) Below are a few code samples in **cURL, C\#, and node.js \(please note that `client_id`, `client_secret`, and `audience,`** **will be emailed to you - these are just example values.**

```bash
curl --request POST \
  --url 'https://betterpt-dev.auth0.com/oauth/token' \
  --header 'content-type: application/json' \
  --data '{"client_id":"XXXXXXXXXXXX","client_secret":"XXXXXXXXXXXXXXXXXX","audience":"https://audience.env/auth","grant_type":"client_credentials"}'
```

```csharp
var client = new RestClient("https://betterpt-dev.auth0.com/oauth/token");
var request = new RestRequest(Method.POST);
request.AddHeader("content-type", "application/json");
request.AddParameter("application/json", "{\"client_id\":\"XXXXXXXXXXXX\",\"client_secret\":\"XXXXXXXXXXXXXXXXXXXXXXXXXX\",\"audience\":\"https://audience.env/auth\",\"grant_type\":\"client_credentials\"}", ParameterType.RequestBody);
IRestResponse response = client.Execute(request);
```

```javascript
var request = require("request");

var options = { method: 'POST',
  url: 'https://betterpt-dev.auth0.com/oauth/token',
  headers: { 'content-type': 'application/json' },
  body: '{"client_id":"XXXXXXXXXXXXXXXXXXXX","client_secret":"XXXXXXXXXXXXXXXXXXXXXXXXXXXXX","audience":"https://audience.env/auth","grant_type":"client_credentials"}' };

request(options, function (error, response, body) {
  if (error) throw new Error(error);

  console.log(body);
});
```

**2. Token Response**

```bash
{
  "access_token": "XXXXXXXXXXXXI6IkpXVCIsImtpZCI6IlF6UXlRemsxT0RrNE5rSTRRME5ETWpFeU1rUTFSamt4T1VNMU1EZzVRalU0TmpRNE1qSTBOUSJ9.eyJpc3MiOiJodHRwczovL2JldHRlcnB0LWRldi5hdXRoMC5jb20vIiwic3ViIjoiSFQ5ZU9JazE4TXI2TmtDQzdOVkhicFF0MTVibnkxZkdAY2xpZW50cyIsImF1ZCI6Imh0dHBzOi8vYmV0dGVycHQtY2xpbmljaWVudC5zdGFnaW5nL2F1dGgiLCJpYXQiOjE1ODYyMzE3ODksImV4cCI6MTU4NjMxODE4OSwiYXpwIjoiSFQ5ZU9JazE4TXI2TmtDQzdOVkhicFF0MTVibnkxZkciLCJzY29wZSI6Im11dGF0ZTpwYXJ0bmVyVmlkZW9Sb29tcyBxdWVyeTpwYXJ0bmVyU2Vzc2lvbnMiLCJndHkiOiJjbGllbnQtY3JlZGVudGlhbHMifQ.YNT4rUxF11SX4QFz6aIxhL8-Fn9ARpHs6pOP_0dFbaRHWxR487YtPKryVH7z6xZYiEKWHWdBkCBIbrVFsxTu-aza9acYbvHHjnfMbzPKwSmjAX-VKsbayNOJ2NJJnF-VmOgoqoZPQcWWkFY5Y6AmUTDEsmje7EKhtAg73E41YqNBnuJFwkisCZHdhv4qtNGUbszkB2qPxCNwDjPGrv6MMjsr23tA5iT-GLqGeRHAUN7c1-n9iFG6go286Tufd89a2cfFzt2ykPdQ3gJX0O0x6Gwzdz6xV0Ifr2ku5KEyH5ba6BdtJaRJ2OesihAxo_zem-t6V3SHjimHF58r0MQz5A",
  "token_type": "Bearer"
}
```

**3. Using the token**

Here is how you set and use the token \(GraphQL example in progress\):

```bash
curl --location --request POST 'http://api-staging-k8s.betterpt.com/v0/graphql-public/' \
--header 'authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IlF6UXlRemsxT0RrNE5rSTRRME5ETWpFeU1rUTFSamt4T1VNMU1EZzVRalU0TmpRNE1qSTBOUSJ9.eyJpc3MiOiJodHRwczovL2JldHRlcnB0LWRldi5hdXRoMC5jb20vIiwic3ViIjoiNWdaTkZGUlZmMnQ2cEZ3UWMzNzdvaGVLZVFkYUtUdmhAY2xpZW50cyIsImF1ZCI6Imh0dHBzOi8vYmV0dGVycHQtY2xpbmljaWVudC5zdGFnaW5nL2F1dGgiLCJpYXQiOjE1ODY4MDE2NzIsImV4cCI6MTU4Njg4ODA3MiwiYXpwIjoiNWdaTkZGUlZmMnQ2cEZ3UWMzNzdvaGVLZVFkYUtUdmgiLCJzY29wZSI6Im11dGF0ZTpwYXJ0bmVyVmlkZW9Sb29tcyBxdWVyeTpwYXJ0bmVyU2Vzc2lvbnMiLCJndHkiOiJjbGllbnQtY3JlZGVudGlhbHMiLCJwZXJtaXNzaW9ucyI6WyJtdXRhdGU6cGFydG5lclZpZGVvUm9vbXMiLCJxdWVyeTpwYXJ0bmVyU2Vzc2lvbnMiXX0.ingfAArqpxVTspCtoFHFJ4brKl9CZOmeg3SPGRUQDE39usAZZZDzdZw1XM2FrDhC-b3vZ1uL_RSs_mrDiwBggSJ8czjLVbWZ6Y5gPx4USSR_Rg35m8JEedDxX1m9Fltz2Fa4809QDpJVWtthtTuUz3hfeNrAQjrT2J_azkj-NLwmeYzLIhHIIHCAUndFupAoMYWhvevVsDP7FJ70qmdcXYtKahPbpCbcwcVR0JsRzNBkBkwP6H6rAqtdGeGVfTkatCLOBKqYGyTpyIcA6mlX39AWJ1MfwePEsexR5XbiivTpTRvrS3PvnX_wg9Y7EeYU8sEckchZKDOKKsD384eoDA' \
--header 'Content-Type: application/json' \
--data-raw '{"query":"mutation {\n  createPartnerVideoRoom(input: { startTime: \"2020-12-03T10:15:30Z\", displayName: \"Bob the PT\", timeZone: \"America/New_York\" } ) {\n    patientLink\n    providerLink\n    startTime\n    displayName\n    timeZone\n    uid\n  }\n}","variables":{}}'
```

**4. Reuse the access token**

{% hint style="danger" %}
NOTE: You **must check the access token's expiration and use it if it is still valid** **in production** \(and preferably in staging\) or you will be **severely rate-limited and your video rooms will not be successfully created.**
{% endhint %}

Example of reusing access token and checking expiration:

_in this_ example _we are using AWS DynamoDB and AWS Secrets Manager, your implementation may differ._

```typescript
const secretsManager = new AWS.SecretsManager();
const documentClient = new AWS.DynamoDB.DocumentClient({ apiVersion: '2012-08-10' });

// JWT config
const { domain, clientId, audience, dataKey } = config;

// Fetches secrets from AWS Secrets Manager
const getSecrets = () =>
  secretsManager
    .getSecretValue({ SecretId: `secrets-store` })
    .promise()
    .then(secretsRawData => JSON.parse(secretsRawData.SecretString));

// the token service
export const tokenService = {
  // Determines if the token is expired based on the expiration date
  isTokenExpired(encodedToken: string): boolean {
    const decoded: any = jwtDecode(encodedToken);
    const expirationTimeOnToken = datefns.fromUnixTime(decoded.exp);
    const currentTime = new Date();
    const isTimeOnTokenBeforeCurrentTime = datefns.isBefore(expirationTimeOnToken, currentTime);

    console.log(`Time on the token ${expirationTimeOnToken}`);
    console.log(`Is token expired? ${isTimeOnTokenBeforeCurrentTime}`);

    return isTimeOnTokenBeforeCurrentTime;
  },

  // Get the token from the data store (in this case a DynamoDB Table)
  async getTokenFromDataStore(): Promise<string | null> {
    const params = {
      TableName : 'TokenDataStore',
      Key: { environment: dataKey }
    };
    const res = await documentClient.get(params).promise();
    return res && res.Item && res.Item.token;
  },

  // Updates the token in the data store
  async updateTokenInDataStore(token: string): Promise<void> {
    var params = {
      TableName : 'TokenDataStore',
      Item: {
         environment: dataKey,
         token: token,
         updatedAt: new Date().toISOString(),
      }
    };
    await documentClient.put(params).promise();
  },

  // Fetches a new token from Auth0 using the 
  // node.js Auth0 SDK.
  async fetchNewTokenFromProvider(): Promise<string> {
    const secrets = await getSecrets();
    const { CLIENT_SECRET } = secrets;

    const auth0Client = new auth0.AuthenticationClient({
      domain,
      clientId,
      clientSecret: CLIENT_SECRET,
    });

    const credentialsResponse = await auth0Client.clientCredentialsGrant({ audience });

    console.log('Fetched new token from Auth0');

    return credentialsResponse && credentialsResponse.access_token;
  },

  // Get the access token, refreshed or otherwise
  async getAccessToken(): Promise<string> {
    const accessToken = await this.getTokenFromDataStore();

    if (accessToken && !this.isTokenExpired(accessToken)) {
      console.log('Using existing access token!');
      return accessToken;
    }

    const refreshedToken = await this.fetchNewTokenFromProvider();
    await this.updateTokenInDataStore(refreshedToken);
    return refreshedToken;
  }
}
```

