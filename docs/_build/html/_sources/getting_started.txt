===============
Getting Started
===============


|
|

Creating an API Key
===================
	Access to the API is available to everyone with a BitBlox Developer account. Once you have logged in, visit the `app management page <http://testblox.info/applications>`_ to generate a new API key or manage existing keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|

Authentication
==============
	The API is secured with OAuth 2. You must use your client key and secret to sign requests when accessing the API. There are many `established libraries <http://oauth.net/2/>`_ that will take care of authenticating calls for you. There is no need to provide an access token as our API endpoints do not yet support granting access to a users private data.

	| POST http://testblox.info/oauth/v2/token?
	| client_id=CLIENT_ID&
	| client_secret=CLIENT_SECRET&
	| grant_type=password&
	| username=USERNAME&
	| password=PASSWORD

	For more examples, please view our `sample code`_.


|
|

Making Requests
===============
	After authenticating, you can make requests. To make a request you simply need to point to ``http://testblox.com/api/``. It’s really that simple!

	For reference, here is a list of our `API Explorer <http://explorer.testblox.info/doc/api>`_, and `sample code`_.

|
|

Supported File Formats and HTTP Responses
=========================================
	Every request will return a JSON response or an error response.


	**Examples:**

	- ``200 Success`` - Returned when all ok
	- ``201 Created`` - Returned when the object is created
	- ``400 Bad Request`` - Returned when an error has occurred while object creation
	- ``404 Not found`` - Returned when unable to find object
	- ``401 Unauthorized`` - Unauthorized access
	- ``429 Too many requests`` - Return when a client hits the rate limit (limit=7200, period=3600)

|
|

Pricing
=======
	We have plans of all sizes, and can customize plans to meet your needs. Please visit the `developers page <http://testblox.info/developers>`_ for more information on our API pricing.

|
|


Sample Code
===========

	**Python:**

	.. code-block:: python

		import requests
		from requests_oauthlib import OAuth1

		auth = OAuth("your-api-key", "your-api-secret")
		endpoint = "http://api.thenounproject.com/icon/1"

		response = requests.get(endpoint, auth=auth)
		print response.content

	|

	**Ruby:**

	.. code-block:: ruby

		require "oauth"

		consumer = OAuth::Consumer.new("your-api-key", "your-api-secret")
		access_token = OAuth::AccessToken.new consumer
		endpoint = "http://api.thenounproject.com/icon/1"

		response = access_token.get(endpoint)
		puts response.body

|
|


Multipass
=========

Let's say you are the owner of a successful website forum. All of your users must log in to the forum to contribute. Members of your forum can then purchase a forum t-shirt through your Shopify store. Unfortunately, your users have to log in to the forum first and then log in to your Shopify store before they can purchase a t-shirt.

Multipass login is for store owners who have a separate website and a Shopify store. It redirects users from the website to the Shopify store and seamlessly logs them in with the same email address they used to sign up for the original website. If no account with that email address exists yet, one is created. There is no need to synchronize any customer databases.

 	- ``Information`` - The Multipass login feature is only available to Remote Editor.
	- ``Request url`` - http://{project_name}.{your_domain}/multipass/login/{token}

	**PHP:**

	.. code-block:: php

		<?php

		class Multipass {

			private $signature_key;

			private $encryption_key;

			private $init_vector;

			public function __construct($secret_key)
			{
				$key_material = hash("SHA256", $secret_key, true);

				$this->encryption_key = substr($key_material, 0, 16);
				$this->signature_key  = substr($key_material, 16, 16);

				$iv_material = hash("SHA256", $this->encryption_key, true);

				$this->init_vector = substr($iv_material, 0, 16);
			}

			/**
			 * Converts and signs a PHP object or array into a JWT string.
			 *
			 * @param object|array  $payload    PHP object or array
			 *
			 * @return string A signed JWT
			 *
			 * @uses jsonEncode
			 * @uses urlsafeB64Encode
			 */
			public function encode($payload)
			{
				$segments = array();

				$segments[] = $this->urlsafeB64Encode($this->encrypt(json_encode($payload), $this->encryption_key, $this->init_vector));
				$signing_input = implode('.', $segments);

				$signature = $this->sign($signing_input, $this->signature_key);
				$segments[] = $this->urlsafeB64Encode($signature);

				return implode('.', $segments);
			}

			/**
			 * Sign a string with a given key and algorithm.
			 *
			 * @param string            $msg    The message to sign
			 * @param string|resource   $key    The secret key
			 *
			 * @return string An encrypted message
			 *
			 */
			private function sign($msg, $key)
			{
				return hash_hmac('SHA256', $msg, $key, true);
			}

			/**
			 * Encode a string with URL-safe Base64.
			 *
			 * @param string $input The string you want encoded
			 *
			 * @return string The base64 encode of what you passed in
			 */
			private function urlsafeB64Encode($input)
			{
				return str_replace('=', '', strtr(base64_encode($input), '+/', '-_'));
			}

			public function encrypt($json_payload, $encryption_key, $init_vector)
			{
				return openssl_encrypt($json_payload, 'AES-128-CBC' , $encryption_key, OPENSSL_RAW_DATA, $init_vector);
			}
		}

	|

	.. code-block:: php

		<?php
 			 date_default_timezone_set("UTC");

			 $date = new \DateTime();

			 $user_data = [
				 "email" => "user email",
				 "created_at" => $date->format(\DateTime::ISO8601),
				 "return_to" => "redirect to"
			 ];

			 $multipass = new Multipass("application secret key");
			 $token = $multipass->encode($user_data);

	|