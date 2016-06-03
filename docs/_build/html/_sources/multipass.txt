=========
Multipass
=========

Let's say you are the owner of a successful website forum. All of your users must log in to the forum to contribute. Members of your forum can then purchase a forum t-shirt through your Shopify store. Unfortunately, your users have to log in to the forum first and then log in to your Shopify store before they can purchase a t-shirt.

Multipass login is for store owners who have a separate website and a Shopify store. It redirects users from the website to the Shopify store and seamlessly logs them in with the same email address they used to sign up for the original website. If no account with that email address exists yet, one is created. There is no need to synchronize any customer databases.

.. note::
	The Multipass login feature is only available for Remote Editor users.

``Request url: http://{project_name}.{your_domain}/multipass/login/{token}``

|
|

Example implementation
======================

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