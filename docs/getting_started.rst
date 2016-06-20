==============
Getting Started
==============


Api Credentials
=================

Access to the API is available to everyone with a Noun Project account (including users with Playground accounts). Once you have logged in, visit the app management page to generate a new API key or manage existing keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.


Authentication
=================


Your app cannot read BitBlox data without authenticating first. It must get permission from a user before gaining access to any of the resources in the REST API. This guide will walk you through the authorization process (described in greater detail by the `OAuth 2.0 specification <https://tools.ietf.org/html/rfc6749>`_).

|
|

API Credential Rotation
-----------------

Access to the API is available to everyone with a BitBlox Developer account. Once you have logged in, visit the `remote editor management page <http://www.bitblox.me/developer/editors>`_ to manage keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|

Request access token
-----------------

For creating access token: ``POST http://api.bitblox.me/oauth/token`` with the following parameters:

	- ``client_id (required)``: The API key for your app
	- ``client_secret (required)``: The Secret Key for your app
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token
	- ``email (required)``: Client email.
	- ``password (required)``: Client password.

.. 	note::
	The access token is temporary, and can only be used for one hour after it has been generated.

|
|

	**PHP**

	.. code-block:: php

		<?php

		  define('GRANT_TYPE_PASSWORD', 'password');

		  $client_id     = "{CLIENT_ID}";
		  $client_secret = "{CLIENT_SECRET}";
		  $email         = "{CLIENT_EMAIL}";
		  $password      = "{CLIENT_PASSWORD}";

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
-----------------

For each access token stored by your application, refresh it by requesting an access token using your new shared secret and the refresh token:
``POST http://api.bitblox.me/oauth/token``
with the following parameters:

	- ``client_id (required)``: The API key for your app
	- ``client_secret (required)``: The new Shared Secret for your app
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token
	- ``refresh_token (required)``: The refresh token you created from your app’s page in the Partners dashboard

.. 	note::
	The refresh token is temporary, and can only be used for one hour after it has been generated.

|
|

	**PHP**

	.. code-block:: php

		<?php

		  define('GRANT_TYPE_REFRESH_TOKEN', 'refresh_token');

		  $client_id     = "{CLIENT_ID}";
		  $client_secret = "{CLIENT_SECRET}";
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
-----------------

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
-----------------------

Automated tasks that pause and resume are the best way to stay within the API call limit since you don't need to wait while things get done.

This article will show you how to tell your program to take small pauses to keep your app a few API calls shy of the API call limit and to guard you against a **429 - Too Many Requests error.**

|
|

How to avoid the 429 error
---------------------------

Some things to remember:

1. You can check how many calls you've already made using the BitBlox header that was sent in response to your API call:

- ``X-RateLimit-Limit:7200``
- ``X-RateLimit-Remaining:7199``
- ``X-RateLimit-Reset:1464952507``

Keep in mind that X will decrease over time. If you see you're at 39/40 calls, and wait 10 seconds, you'll be down to 19/40 calls.

2. You can only update one page or project with one API call.