---

title: Integrating Shopee into Ruby on Rails app - Part 1
date: 2018-04-16 06:58 UTC
tags:

---

Hello! In this article, we will be looking at the basic part of integrating Shopee - The leading online shopping platform in Southeast Asia and Taiwan into our Ruby on Rails app.

I will be demonstrating on making a simple successful request to one of Shopee's endpoint. I will be using `shopee.shop.GetShopInfo` endpoint to get the registered shop info. Shopee will provide the following information (below) to merchants. These information is required in order to make a request to Shopee API.

- Partner ID
- Shop ID
- Secret Key

#### Checkpoints when making a request
- Ensure that `POST` method is being at all times. Shopee specified that all API's should use `POST`.
- Ensure Content Type is being set to `application/json` in the HTTP Header
- Ensure the HTTP Body contains the following at all times.
  - partner_id
  - shopid
  - timestamp

- The HTTP Body should look something like `{"partner_id": 123456, "shopid": 123456, "timestamp": 1523865691}`

#### Authenticating a Request
Having those in place, we are read to authenticate the request. The main part of successfully authenticating a request to Shopee is to generate an auth signature correctly. The signature is calculated by passing the signature base string and signing key to the HMAC-SHA256 hashing algorithm.

 - Signature Base String
    We first need to get the signature base string by concatenating the url and the data.

        BASE_URL = "https://partner.shopeemobile.com/api/v1"
        API = /shop/get
        DATA = {"partner_id": "PARTNER_ID_PROVIDED_BY_SHOPEE_IN_INTEGER", "shopid": "SHOP_ID_PROVIDED_BY_SHOPEE_IN_INTEGER", "timestamp": DateTime.now.to_i}.to_json
        signature_base_url = BASE_URI + API + "|" +  DATA

 - Generating Auth Signature
    Once the Signature Base String is obtained, we can simply generate the auth signature by simply passing the `Secret Key` provided by Shopee and the Signature Base String to the hashing algorithm.

        auth_signature = OpenSSL::HMAC.hexdigest(OpenSSL::Digest.new('sha256'), "SECRET KEY PROVIDED BY SHOPEE", signature_base_url)

### Making the API Call
Putting it all together, simple use `HTTParty` to make a post request and pass in the necessary headers.

        HTTParty.post(
          BASE_URI + API,
          :headers => {
            'Content-Type' => 'application/json',
            'Accept' => 'application/json',
            "Authorization": auth_signature
            }, :body => DATA)

Congrats! You can now use any Shopee API. Just simply pass in any additional parameters to the body as required by the respective API and you are good to go!

Happy Coding!
