==============
API Call Limit
==============

The API call limit operates using a "leaky bucket" algorithm as a controller. This allows for infrequent bursts of calls, and allows your app to continue to make an unlimited amount of calls over time. The bucket size is 40 calls (which cannot be exceeded at any given time), with a "leak rate" of 2 calls per second that continually empties the bucket. If your app averages 2 calls per second, it will never trip a 429 error ("bucket overflow"). To learn more about the algorithm in general, click here.

Your API calls will be processed almost instantly if there is room in your "bucket". Unlike some integrations of the leaky bucket algorithm that aim to "smooth out" (slow down) operations, you can make quick bursts of API calls that exceed the leak rate. The bucket analogy is still a limit that we are tracking, but your processing speed for API calls is not directly limited to the leak rate of 2 calls per second.

**Are you going over the API limit?**

Automated tasks that pause and resume are the best way to stay within the API call limit since you don't need to wait while things get done.

This article will show you how to tell your program to take small pauses to keep your app a few API calls shy of the API call limit and to guard you against a 429 - Too Many Requests error.

**How to avoid the 429 error**
Some things to remember:
- You can check how many calls you've already made using the BitBlox header that was sent in response to your API call: CALL_LIMIT (lists how many calls you've made for that particular shop)

Keep in mind that X will decrease over time. If you see you're at 39/40 calls, and wait 10 seconds, you'll be down to 19/40 calls.

**Example**

This example would only happen in "edge cases" because unless the products in your shop are all out of stock you likely won't need to use a throttling mechanism in the way shown above to delete out of stock products. Nevertheless, there will be situations where it becomes critical to 'pause and resume' your program. Say you want to edit the price of all the products in your shop.