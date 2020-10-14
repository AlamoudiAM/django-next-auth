# Login via DRF
update [[...nextauth].js](https://github.com/nextauthjs/next-auth-example/blob/main/pages/api/auth/%5B...nextauth%5D.js)

```javascript
const options = {
  providers: [
    Providers.Credentials({
      // ...
      authorize: async (credentials) => {
        var drfTokenResponse = undefined;
        try {
          drfTokenResponse = await axios.post('http://127.0.0.1:8000/api/token-auth/', {
            username: credentials.username,
            password: credentials.password
          });
        } catch (error) {
          console.error(error);
        }
        const drfToken = drfTokenResponse?.data?.token;
        if (drfToken) {
          // Any object returned will be saved in `user` property of the JWT
          return Promise.resolve(/* USER */ true);
        } else {
          return Promise.resolve(null)
        }
      }
    })
  ],
  callbacks: { 
    session: async (session, user, sessionToken) => {
      console.log(user);
      delete session['user'];
      session = {...session, ...user}
      // TODO: get user permissions and groups from DRF and store them in session
      return Promise.resolve(session);
    },
    jwt: async (token, user, account, profile, isNewUser) => {
      // pass user info to be accessible by session
      if (user)
        token.drf = user
      return Promise.resolve(token);
    }
  },
}
```
