# fork
Repository for Authentication API doc work

OVERVIEW

Authentication is the act of validating an attribute of a single piece of data (a datum) claimed as 'true' by an entity. Authentication can pose special problems with electronic communication, such as vulnerability to man-in-the-middle attacks, whereby a third party taps into the communication stream, and poses as each of the two other communicating parties, in order to intercept information from each. 

Various systems have been invented to allow authors to provide a means for readers to reliably authenticate that a given message originated from or was relayed by them. These involve authentication factors such as a shared secret like a passphrase, in the content of the message. 

Another example of a widely used authentication technique is use of an electronic signature. This involves using the public-key infrastructure to cryptographically verify the message signature of a particular private key holder.

SPARK AUTHENTICATION

Spark authentication converts the app key and app secret allocated during app registration into a guest token or an access token. The tokens are codes whose validity extends for the duration of a single session. The codes generated grant access to the Spark API, identifying your app, and when relevant, prove that a legitimate Spark member has logged in from your app.

Guest tokens grant access to all public data on the Spark Portal. Access tokens grant access to public data and authenticate the user, giving access to their private-member (Spark-user) data. 

NOTE: To generate either a GUEST or ACCESS token, your app must be registered on the developer's portal. Also required is that the member whose data is being accessed logs in or registers with your app.

This diagram provides a physical representation of the Spark authentication process:

//Authentication Flow Diagram To Be Placed Here//

HTTP HEADERS

Spark authentication API require two HTTP headers: Content-Type and Authorization. 

Content-Type: application/x-www-form-urlencoded

Authorization: Basic {base64 encoding of "app key:app secret"}

The Authorization header includes two essential steps:

1.Get the "app key" and "app secret" values from the app registration's API Keys tab. 

2. Base64 encodes the app key and app secret as a single string separated by a colon (app key:app secret). For Base64 encoding see http://www.base64encode.org/.

  Example: App key = 456, app secret = 789. You Base64 encode 456:789 and get NDU2Ojc4OQ==. 
  The header is Authorization:     Basic NDU2Ojc4OQ==.
  
  SAMPLE AUTORIZATION CODE

@@ -1 +1,21 @@
-# test-davidnov
+# spark-auth-sample-python
+
+This is a sample code for using spark authentication api.
+
+## Create application on Spark developer portal
+
+Sign up to [Spark developer portal](https://spark.autodesk.com/developers/) and create a new application [here](https://spark.autodesk.com/developers/getStarted).
+
+Set you Callback URL to http://localhost:8089/callback.
+
+## Configuration
+
+Open the SparkOAuth.py file:
+
+ 1. Replace CONSUMER_KEY with your App Key.
+
+ 2. Replace CONSUMER_SECRET with your App Secret.
+
+## Usage
+
+To start using this sample you just have to browse http://localhost:8089/signin

ABOUT OAUTH 2.0 (REFERENCE)

NOTE: This section provides explanations and access token generation instructions related to OAUTH 2.0. We provide it for your general edification, and in the case that the developer/member needs assistance with his part of authorization. The full URL is:

http://docs.sparkauthentication.apiary.io/#reference/oauth-2.0/guest-token

Access Token Spark provides two methods for generating access tokens: Explicit log-in and Implicit log-in. Implicit Log-In is an easy to implement client-side authentication that is less secure. Use implicit log-in to get an app working quickly or if your app is entirely client-side and may expose API parameters. 

Explicit Log-In is more secure than implicit log-in but requires a server-side section for apps written in programming languages that expose API parameters.

ACCESS TOKEN GENERATION

These steps are needed to produce the actual ACCESS token:

Implicit Log-In	                                Explicit Log-In
1.	Member logs-in/registers from your app.	    Member logs-in/registers from your app.
2.	Callback URL is sent an access token.	      Callback URL is sent an authorization code.
3.	Not required.	                              API Call: Generate access token. The response returns an access token.

NOTE: Access tokens generated explicitly can be refreshed when they expire (after two hours), using the provided refresh token: No further log-in is required by the member.

INSTRUCTIONS FOR MEMBER LOG IN FROM YOUR APP 

Launch the following web page from your app (redirect the member), using a browser/window control and replacing {app key} and {callback URL} with your own values (see the table below for parameter details):

Implicit Log-in: https://sandbox.spark.autodesk.com/api/v1/oauth/authorize?response_type=token&client_id={app key}&redirect_uri={callback URL}

Explicit Log-In: https://sandbox.spark.autodesk.com/api/v1/oauth/authorize?response_type=code&client_id={app key}&redirect_uri={callback URL}

PARAMETERS USED 

These parameters are used during member login - 

Parameter	    Required/Optional	    Description
response_type	Required	            Use code if you are calling the Access Token API from a server or token for an implicit                                      log-in.
client_id	    Required	            The app key provided when you registered your app.
redirect_uri	Required	            The full path of your callback URL as entered in the app registration details. Note that                                     this must be a fully qualified URL and not a relative path.
state	        Optional	            The value provided in &state will be returned to the callback URL. It should be a unique                                     identifier you create to verify the callback comes from your original request.

LOADING THE CALLBACK URL

After the member logs-in, Autodesk loads the callback URL passing parameters in a query string. The parameters passed depend on the log-in method chosen:

Implicit Log-In: Your access token and its expiry time (in seconds) are returned in the query string ("yNPA3taQaNy" in this example): http://localhost/callback.html#expires_in=7199&access_token=yNPA3taQaNy

Explicit Log-In: An authorization code is returned for use in the API call ("xyz" in this example): http://localhost/callback.url?code=xyz

EXPLICIT LOG-IN ONLY: CALL THE GENERATE ACCESS TOKEN API

This step is only used for explicit log-ins. Before making the Generate an Access Token API call, do the following:

a. Prepare the authorization header
  
  Base64 encode the app key and app secret as a single string separated by a colon in the format app key:app secret. See the   notes below for more information.
  
b. Prepare the parameters

&code parameter: Extract the authorization code passed to the callback URL ("xyz" in the example) for use in the &code parameter. Authorization codes are valid for 90 seconds.

&redirect_uri parameter: Use the same value you passed in step 1, when you loaded the Spark login page.
&grant_type and &response_type are constants and don't change.

c. If you are generating your access token in the documentation - place the parameters in the Console's body section and the base64 code in the Console's headers section.

Now prepare the HTTP Authorization Header, as explained "HTTP HEADERS" above.

GUEST TOKEN GENERATION INSTRUCTIONS

Generate a guest token. This type of token allows restricted, read-only access to APIs returning public data such as public assets and member public profiles.

The Guest Token API request must have an Authorization header in the format "Authorization: Basic {base64 code}".{base64 code} is the Base64 encoding of the app key and app secret as a single string separated by a colon in the format app key:app secret. 
    
    Example: App key is 456 and app secret is 789. You Base64 encode 456:789 and get NDU2Ojc4OQ==, the header is                 Authorization: Basic NDU2Ojc4OQ==. App key and app secret are codes provided in app registration. 
    
The response "expires_in" field gives the validity of the token in seconds. The timer starts running from the response receipt time.

NOTE: Guest, access and refresh response fields can be found at the same URL: http://docs.sparkauthentication.apiary.io/#reference/oauth-2.0/guest-token


