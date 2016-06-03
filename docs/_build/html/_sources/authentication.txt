==============
Authentication
==============


API Keys
========

	Access to the API is available to everyone with a BitBlox Developer account. Once you have logged in, visit the `remote editor management page <http://bodnar.info/developer/editors>`_ to manage keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|


Creating Token Example
======================

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

		  $url = 'http://bodnar.info/oauth/v2/token?'.$query_string;

		  $handler = curl_init();

		  curl_setopt($handler, CURLOPT_URL, $url);
		  curl_setopt($handler, CURLOPT_RETURNTRANSFER, 1);

		  $output = curl_exec($handler);

		  echo $output;

		  curl_close($handler);

		?>

|
|


Request new access tokens
=========================

For each access token stored by your application, refresh it by requesting an access token using your new shared secret and the refresh token:
GET https://bodnar.info/oauth/v2/token?
with the following parameters:

	- ``client_id (required)``: The API key for your app
	- ``client_secret (required)``: The new Shared Secret for your app
	- ``refresh_token (required)``: The refresh token you created from your app’s page in the Partners dashboard

.. 	note::
	The refresh token is temporary, and can only be used for one hour after it has been generated.

Example Implementations
=======================

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

		  $url = 'http://bodnar.info/oauth/v2/token?'.$query_string;

		  $handler = curl_init();

		  curl_setopt($handler, CURLOPT_URL, $url);
		  curl_setopt($handler, CURLOPT_RETURNTRANSFER, 1);

		  $output = curl_exec($handler);

		  echo $output;

		  curl_close($handler);
		?>
