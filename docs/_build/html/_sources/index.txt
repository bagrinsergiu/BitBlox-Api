===============
Getting Started
===============

----------------------------

|
|

Creating an API Key
===================
	Access to the API is available to everyone with a Noun Project account (including users with Playground accounts). Once you have logged in, visit the `app management page <http://thenounproject.com/developers/apps/>`_ to generate a new API key or manage existing keys. It is important that you treat this key as if it were a secret password. With an API key and secret, anyone can access endpoints from your account.

|
|

Authentication
==============
	The API is secured with OAuth 1.0a. You must use your client key and secret to sign requests when accessing the API. There are many `established libraries <http://oauth.net/code/>`_ that will take care of authenticating calls for you. There is no need to provide an access token as our API endpoints do not yet support granting access to a users private data.

	For more examples, please view our `sample code`_.

.. note::
* Nonce must be a minimum of 8 characters in length.

|
|

Making Requests
===============
	After authenticating, you can make requests. To make a request you simply need to point to ``http://api.thenounproject.com``. It’s really that simple!

	For reference, here is a list of our `public API endpoints <documentation.html>`_, `API Explorer <http://api.thenounproject.com/explorer>`_, and `sample code`_.

|
|

Supported File Formats and HTTP Responses
=========================================
	Every request will return a JSON response or an error response.


	**Examples:**

	- ``200 Success`` - returns data as JSON in the response body.
	- ``404 Not Found`` - may return extra error description in response body.
	- ``401 Unauthorized`` - may return extra error description in response body.

|
|

Pricing
=======
	We have plans of all sizes, and can customize plans to meet your needs. Please visit the `developers page <http://thenounproject.com/developers/>`_ for more information on our API pricing.

|
|

Unacceptable Uses
=================
	The Noun Project API is designed to empower developers with a visual language. Using the Noun Project API inappropriately will result in the review and removal of your API keys.

	**This includes but is not limited to:**

		- Distributing icons.
		- Reselling content.
		- Exploiting The Noun Project users or content.
		- Replicating The Noun Project.

|
|

Sample Code
===========

	**Python:**

	.. code-block:: python

		import requests
		from requests_oauthlib import OAuth1

		auth = OAuth1("your-api-key", "your-api-secret")
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

Community Tools
==================
	The following is a list of unofficial tools by our community to help you along with development. If you experience any issues, please reach out to the individual as we're not affiliated with these projects.
	
	**Wrappers:**
	
		- `Node.js Wrapper  <https://github.com/rosshettel/the-noun-project>`_ by Ross Hettle
		- `Ruby Wrapper <https://github.com/TailorBrands/noun-project-api>`_ by Tailor Brands
		- `PHP Wrapper <https://github.com/onassar/PHP-TheNounProject>`_ by Oliver Nassar
	
	|
	
	**Examples:**
	
		- `Node.js Example  <https://gist.github.com/hirobert/3bca9fd56b9b4418b1ca>`_
		- `PHP Example  <https://gist.github.com/hirobert/710f2e22ed803dc34cc0>`_

|
|

