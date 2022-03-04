# Google Ads API refresh token helper

A tiny web interface to help you get started with your Google Ads API project.

For many OAuth2 workflows, you need to obtain something called a refresh token, which requires a browser based workflow. However, once you have obtained your long-lived refresh token, you may not need a browser for any further interactions with the Google Ads API.

For example, if you have a manager account that has multiple client accounts linked to it. Once you've obtained the refresh token, you should be able to interact programatically with the Google Ads API from a server without needing to involve a web browser.

See below for an example using the the [`google-ads-api`](https://github.com/Opteo/google-ads-api) library.
```js
const { GoogleAdsApi } = require("google-ads-api");

// Google Cloud Platform Dashboard -> APIs & Services -> Credentials -> OAuth 2.0 Client IDs
const clientId =
  "123456789-d8989daskfjoiwqjasdfw23f.apps.googleusercontent.com";
// Find this in the same location as clientId
const clientSecret = "ABCDEFG-398hfa2fioasdlkfjasd";
// Google Ads Dashboard -> Tools and Settings -> API Center
const developerToken = "gsfgadfs6a8-gadsf7as6dfads2";
// customer id of the client account. this should be the same as loginCustomerId if not acting on behalf of a client.
const customerId = "987654321";
// customer id of the manager account
const loginCustomerId = "135798642";
// I have no idea what this property is for. Not needed in most scenarios?
const linkedCustomerId = "";
// Obtain this using the google-ads-api-token-helper! That's what this project is for!
const refreshToken =
  "1//0vp7Ba9cVwTJLCgYIABApGA8SNzG-L9MrSDTzM1na2bxCetrFqKk4GdROh_LC4qiyzftw5QiUtf3s4IOMCyexZkptAnI0dxkZJlY";

const client = new GoogleAdsApi({
  client_id: clientId,
  client_secret: clientSecret,
  developer_token: developerToken,
});

const customer = client.Customer({
  customer_id: customerId,
  login_customer_id: loginCustomerId,
  // linked_customer_id: linkedCustomerId,
  refresh_token: refreshToken,
});

const main = async () => {
  try {
    const campaigns = await customer.query(`
      SELECT
        campaign.name, campaign.status
      FROM
        campaign
    `);
    console.log(campaigns);
  } catch (err) {
    console.log(err);
  }
};

main();
```

It will grant you a `refresh_token` with the `https//www.googleapis.com/auth/adwords` scope.

Hosted on https://johncmunson.github.io/google-ads-api-token-helper/. 

Front-end only -- Only a browser is needed. All network requests are directed towards Google's OAuth services. It will make requests to:
- https://accounts.google.com/o/oauth2/auth (GET request)
- https://accounts.google.com/o/oauth2/token (POST request)

The source code has been kept deliberately simplistic to maintain clarity.
