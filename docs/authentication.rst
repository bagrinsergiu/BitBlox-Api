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
		  $client_id = "public-api-key";
		  $client_secret = "newly-generated-shared-secret";
		  $grant_type = "password";
		  $username = "username";
		  $password = "password"

		  $data = [
		  	'client_id'     => $client_id,
		  	'client_secret' => $client_secret,
			'grant_type'    => $grant_type,
			'username'      => $username,
			'password'      => $password
		  ];

		  $query_string = http_build_query($data) . "\n";

		  $headers = array(
			"Content-Type: application/json",
			"Accept: application/json",
			"Content-Length:" . strlen($data_string)
		  );

		  $handler = curl_init('https://bodnar.info/oauth/v2/token?'.$query_string);

		  curl_setopt($handler, CURLOPT_RETURNTRANSFER, true);
		  curl_setopt($handler, CURLOPT_HTTPHEADER, $headers);

		  $response = curl_exec($handler);
		?>

		<?php echo($response) ?>
		<!-- {"access_token": "abracadabra"} -->

|
|

