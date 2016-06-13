==============
Authentication
==============

Your app cannot read BitBlox data without authenticating first. It must get permission from a user before gaining access to any of the resources in the REST API. This guide will walk you through the authorization process (described in greater detail by the `OAuth 2.0 specification <https://tools.ietf.org/html/rfc6749>`_).

|
|

API Credential Rotation
=======================

Access to the API is available to everyone with a BitBlox Developer account. Once you have logged in, visit the `remote editor management page <http://www.bitblox.me/developer/editors>`_ to manage keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|

Step 1: Request access token
============================

For creating access token: ``GET http://api.bitblox.me/oauth/v2/token?`` with the following parameters:

	- ``client_id (required)``: The API key for your app
	- ``client_secret (required)``: The Secret Key for your app
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token
	- ``username (required)``: Client username.
	- ``password (required)``: Client password.

.. 	note::
	The access token is temporary, and can only be used for one hour after it has been generated.

|
|

Step 2: Example Implementations
===============================

	**PHP**

	.. code-block:: php

		<?php
		  $client_id     = "{CLIENT_ID}";
		  $client_secret = "{CLIENT_SECRET}";
		  $username      = "{USERNAME}";
		  $password      = "{PASSWORD}"

		  $data = [
		  	'client_id'     => $client_id,
		  	'client_secret' => $client_secret,
			'grant_type'    => "password",
			'username'      => $username,
			'password'      => $password
		  ];

		  $query_string = http_build_query($data);

		  $url = 'http://api.bitblox.me/oauth/v2/token?'.$query_string;

		  $handler = curl_init();

		  curl_setopt($handler, CURLOPT_URL, $url);
		  curl_setopt($handler, CURLOPT_RETURNTRANSFER, 1);

		  $response = curl_exec($handler);
		  $response = json_decode($response);

		  $access_token  = $response->access_token;
		  $refresh_token = $response->refresh_token;

		?>

|
|

Step 3: Request new access tokens
=================================

For each access token stored by your application, refresh it by requesting an access token using your new shared secret and the refresh token:
``GET http://api.bitblox.me/oauth/v2/token?``
with the following parameters:

	- ``client_id (required)``: The API key for your app
	- ``client_secret (required)``: The new Shared Secret for your app
	- ``grant_type (required)``: Represents the flow needed for the Client to obtain Access Token
	- ``refresh_token (required)``: The refresh token you created from your app’s page in the Partners dashboard

.. 	note::
	The refresh token is temporary, and can only be used for one hour after it has been generated.

|
|

Step 4: Example Implementations
===============================

	**PHP**

	.. code-block:: php

		<?php
		  $client_id     = "{CLIENT_ID}";
		  $client_secret = "{CLIENT_SECRET}";
		  $refresh_token = "{REFRESH_TOKEN}";

		  $data = [
		  	'client_id'     => $client_id,
		  	'client_secret' => $client_secret,
			'grant_type'    => "refresh_token",
			'refresh_token' => $refresh_token,
		  ];

		  $query_string = http_build_query($data);

		  $url = 'http://api.bitblox.me/oauth/v2/token?'.$query_string;

		  $handler = curl_init();

		  curl_setopt($handler, CURLOPT_URL, $url);
		  curl_setopt($handler, CURLOPT_RETURNTRANSFER, 1);

		  $response = curl_exec($handler);

		  $response = json_decode($response);

		  $access_token  = $response->access_token;
		  $refresh_token = $response->refresh_token;

		?>
