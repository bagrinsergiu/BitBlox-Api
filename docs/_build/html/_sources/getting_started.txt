===============
Getting Started
===============


API Credentials
===============

Access to the API is available to everyone with a BitBlox account. Once you have logged in, visit the `remote editor management page <http://www.bitblox.me/plus/editors>`_ to see your API Key and Secret. It is important that you treat this key as if it were a secret password. With an API Key and Secret, anyone can access endpoints from your account.


Authentication
==============

Your Editor cannot read API data without authenticating first. It must get permission from a user before gaining access to any of the resources in the REST API. This guide will walk you through the authorization process (described in greater detail by the `OAuth 2.0 specification <https://tools.ietf.org/html/rfc6749>`_).

|
|

Request access token
--------------------

For creating access token: ``POST http://api.bitblox.me/oauth/token`` with the following parameters:

	- ``client_id (required)``: The API Key for the app.
	- ``client_secret (required)``: The Secret for the app.
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token.
	- ``email (required)``: User email.
	- ``password (required)``: User password.

.. 	note::
	The access token is temporary, and can only be used for one hour after it has been generated.

|
|

	**PHP**

	.. code-block:: php

		<?php

		  define('GRANT_TYPE_PASSWORD', 'password');

		  $client_id     = "{API_KEY}";
		  $client_secret = "{SECRET}";
		  $email         = "{USER_EMAIL}";
		  $password      = "{USER_PASSWORD}";

		  $data = [
		    'client_id'     => $client_id,
		    'client_secret' => $client_secret,
		    'grant_type'    => GRANT_TYPE_PASSWORD,
		    'email'         => $email,
		    'password'      => $password
		  ];

		  $url = 'http://api.bitblox.me/oauth/token';

		  $curl_handler = curl_init();

		  curl_setopt($curl_handler, CURLOPT_URL, $url);
		  curl_setopt($curl_handler, CURLOPT_RETURNTRANSFER, true);
		  curl_setopt($curl_handler, CURLOPT_POST, true);
		  curl_setopt($curl_handler, CURLOPT_POSTFIELDS, $data);

		  $response = curl_exec($curl_handler);
		  $info     = curl_getinfo($curl_handler);

		  curl_close($curl_handler);

		  $response = json_decode($response);

		  $access_token  = "";
		  $refresh_token = "";

		  if ($response && $info['http_code'] == 200) {
		     $access_token  = $response->access_token;
		     $refresh_token = $response->refresh_token;
		  }

		?>

|
|

Request new access tokens
-------------------------

For each access token stored by your application, refresh it by requesting an access token using your API Key and Secret and the refresh token:
``POST http://api.bitblox.me/oauth/token``
with the following parameters:

	- ``client_id (required)``: The API Key for the app.
	- ``client_secret (required)``: The Secret for the app.
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token.
	- ``refresh_token (required)``: The refresh token you created from your access token request.

.. 	note::
	The refresh token is temporary, and can only be used for one hour after it has been generated.

|
|

	**PHP**

	.. code-block:: php

		<?php

		  define('GRANT_TYPE_REFRESH_TOKEN', 'refresh_token');

		  $client_id     = "{API_KEY}";
		  $client_secret = "{SECRET}";
		  $refresh_token = "{REFRESH_TOKEN}";

		  $data = [
		    'client_id'     => $client_id,
		    'client_secret' => $client_secret,
		    'grant_type'    => GRANT_TYPE_REFRESH_TOKEN,
		    'refresh_token' => $refresh_token
		  ];

		  $url = 'http://api.bitblox.me/oauth/token';

		  $curl_handler = curl_init();

		  curl_setopt($curl_handler, CURLOPT_URL, $url);
		  curl_setopt($curl_handler, CURLOPT_RETURNTRANSFER, true);
		  curl_setopt($curl_handler, CURLOPT_POST, true);
		  curl_setopt($curl_handler, CURLOPT_POSTFIELDS, $data);

		  $response = curl_exec($curl_handler);
		  $info     = curl_getinfo($curl_handler);

		  curl_close($curl_handler);

		  $response = json_decode($response);

		  $access_token  = "";
		  $refresh_token = "";

		  if ($response && $info['http_code'] == 200) {
		     $access_token  = $response->access_token;
		     $refresh_token = $response->refresh_token;
		  }

		?>

When the Token Expires
----------------------

When the token expires, your next API call will fail with the following result:

	.. code-block:: json

		{
		  "error":"invalid_grant",
		  "error_description":"The access token provided has expired."
		}

You’ll need to either refresh your token or create a new one. Our OAuth tokens expire in 3600 seconds (an hour).


API Call Limit
==============


The API call limit operates using a "leaky bucket" algorithm as a controller. This allows for infrequent bursts of calls, and allows your app to continue to make an unlimited amount of calls over time. The bucket size is 40 calls (which cannot be exceeded at any given time), with a "leak rate" of 2 calls per second that continually empties the bucket. If your app averages 2 calls per second, it will never trip a 429 error ("bucket overflow"). To learn more about the algorithm in general, click here.

Your API calls will be processed almost instantly if there is room in your "bucket". Unlike some integrations of the leaky bucket algorithm that aim to "smooth out" (slow down) operations, you can make quick bursts of API calls that exceed the leak rate. The bucket analogy is still a limit that we are tracking, but your processing speed for API calls is not directly limited to the leak rate of 2 calls per second.

|
|

Are you going over the API limit?
---------------------------------

Automated tasks that pause and resume are the best way to stay within the API call limit since you don't need to wait while things get done.

This article will show you how to tell your program to take small pauses to keep your app a few API calls shy of the API call limit and to guard you against a **429 - Too Many Requests error.**

|
|

How to avoid the 429 error
--------------------------

Some things to remember:

1. You can check how many calls you've already made using the BitBlox header that was sent in response to your API call:

- ``X-RateLimit-Limit:7200``
- ``X-RateLimit-Remaining:7199``
- ``X-RateLimit-Reset:1464952507``

Keep in mind that X will decrease over time. If you see you're at 39/40 calls, and wait 10 seconds, you'll be down to 19/40 calls.

2. You can only update one page or project with one API call.



Multipass
=========



Multipass login is for users which was created through BitBlox API. It redirects users from the your website to the BitBlox Editor.

.. note::
	The Multipass login feature is **only** available for BitBlox API `users <http://api.bitblox.me/explorer#get--api-users.{_format}>`_.

|
|

Implementation
--------------

**1. Encode your user information using JSON**

The user information is represented as a hash which must contain at least the email address of the user and a current timestamp (in ISO8601 encoding).

.. code-block:: javascript

	{
  		email: "bob@bitblox.me",
  		created_at: "2016-06-13T15:16:23-04:00",
  		return_to: "redirect_url"
	}

|

**2. Encrypt the JSON data using AES**

To generate a valid multipass login token, you need the Secret given to you in your BitBlox account. The secret is used to derive two cryptographic keys — one for encryption and one for signing. This key derivation is done through the use of the SHA-256 hash function (the first 128 bit are used as encryption key and the last 128 bit are used as signature key).

The encryption provides confidentiality. It makes sure that no one can read the customer data. As encryption cipher, we use the AES algorithm (128 bit key length, CBC mode of operation, random initialization vector).

**3. Sign the encrypted data using HMAC**

The signature (also called message authentication code) provides authenticity. It makes sure that the multipass token is authentic and hasn't been tampered with. We use the HMAC algorithm with a SHA-256 hash function and we sign the encrypted JSON data from step 2 (not the plaintext JSON data from step 1).

**4. Base64 encode the binary data**

The multipass login token now consists of the 128 bit initialization vector, a variable length ciphertext, and a 256 bit signature (in this order). This data is encoded using base64 (URL-safe variant, RFC 4648).

**5. Redirect your user to your website**

Once you have the token, you should trigger a HTTP GET request.

``GET: http://{project_name}.{your_domain}/multipass/login/{token}``

When the request is successful (e.g. the token is valid and not expired), the user will be logged and returned to your website from ``return_to`` param.

The multipass token is only valid within a very short timeframe and each token can only be used once. For those reasons, you should not generate tokens in advance for rendering them into your HTML sites. You should create a redirect URL which generates tokens on-the-fly when needed and then automatically redirects the browser.

|
|


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

Manage DNS Settings
-------------------

1. Log in to your Domain provider dashboard
2. Set CNAME record

+------------+------------+---------------+
| Type       | Name       | Value         |
+============+============+===============+
| CName      | ``*``      | bitblox.me    |
+------------+------------+---------------+