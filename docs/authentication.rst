==============
Authentication
==============


API Keys
========

	Access to the API is available to everyone with a BitBlox Developer account. Once you have logged in, visit the `remote editor management page <http://bodnar.info/developer/editors>`_ to manage keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|

Creating Token
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

