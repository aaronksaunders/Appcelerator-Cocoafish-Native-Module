![alt text](http://clearlyinnovative.com/images/logo_transparent.png "Title")

# Cocoa Fish Native iOS & Android Module

---

##Clearly Innovative Inc
##Aaron K. Saunders
###[http:blog.clearlyinnovative.com](http:blog.clearlyinnovative.com)

---

 Example app.js for Native IOS Module to use with CocoaFish
 I am trying to stay true to the REST API structure that is used
 in most Appcelerator Modules like this, but is is going to be
 a bit of a challenge to wrap all of the functionality in that manner

 You will see by reviewing this example file, that it is very similar
 to the one I posted a few weeks ago that was used for the javascript 
 module I was writing.

 At this point, this is VERY EARLY WORK, please be patient, do not ping
 ping me about bugs... YET but I will be looking for feedback from the 
 community when I get a larger part of the API supported.

 At this point it only supports basic http verbs on your objects.

 Stay Tuned !! More work is coming on this and on the CocoaFish 
 Android Module 

## How it works

Pass in your keys to create the object, you should get your keys from the 
[CocoaFish website](www.cocoafish.com) after you signup and login

    var cocoaFish, options;
    
    options = {
        "consumer_key" : "dIUKXXXXXXXXXXXXXXXXXXXXXXXBb4",
        "secret_key" : "kN0lemXXXXXXXXXXXXXXXXXXXXXXXPva",
        "facebookAppId" : "YOUR FACEBOOK APP ID"
    };
    
    if(Ti.Platform.name == "android") {
        cocoaFish = cocoafishMgr.createCocoaFishMgr(options);
    } else {
        cocoaFish = cocoafishMgr.create(options);
    }
    



Once you have the coocaFish object, you can make API calls using the format described below and in the sample code. Please remember as stated above, **this is a first revision of the code** and it is not perfect.

    cocoaFish.apiCall({
	    "baseUrl" : "users/login.json",
	    "httpMethod" : "POST",
	    "params" : {
		    "login" : "aaron@clearlyinnovative.com",
		    "password" : "password"
	    },
	    success : function(d) {
		    Ti.API.info("responseText is => " + d.responseText);
		    Ti.API.info("metaDataText is => " + d.metaDataText);
		    var user = (JSON.parse(d.responseText)).response.users[0]
		    getPhotos(user.id) 
	    },
	    error : function(d) {
		    Ti.API.error("error is => " + d.errorText);
	    }
    });

On success you get back the meta data and the response text. Below is an example of both

#### Response Text
    {
        "response": {
            "users": [
                {
                    "id": "4ee25dcb356f4e6eac000060",
                    "username": "aaron-b1fbd26c-89b1-49e3-b4c4-f319493324db",
                    "custom_fields": {
                        "coordinates": [
                            [
                                -122.1,
                                37.1
                            ]
                        ]
                    },
                    "updated_at": "2011-12-28T04:35:50+0000",
                    "last_name": "Saunders_1",
                    "created_at": "2011-12-09T19:13:15+0000",
                    "role": "",
                    "email": "aaron@clearlyinnovative.com",
                    "stats": {
                        "storage": {
                            "used": 20896579
                        },
                        "photos": {
                            "total_count": 55
                        }
                    },
                    "first_name": "Aaron",
                    "external_accounts": []
                }
            ]
        },
        "meta": {
            "method_name": "loginUser",
            "code": 200,
            "status": "ok",
            "session_id": "eoXK243J4NRVB97H-Sa23HOSAeU"
        }
    }


#### Meta Data Text
    {
        "meta": {
            "method_name": "loginUser",
            "code": 200,
            "status": "ok",
            "session_id": "eoXK243J4NRVB97H-Sa23HOSAeU"
        }
    }
    
## Uploading a Photo

A photo can be uploaded by passing in a blob or by passing a file object. The API will handle processing the data appropriately before making the API call.

Your code should look something like this to upload the file.

    cocoaFish.apiCall({
        "baseUrl" : "photos/create.json",
        "httpMethod" : "POST",
        "params" : {
            "photo" : Ti.Filesystem.getFile("certificate.png"),
            "tags" : "aaron, nativeIOS, fileObject"
        },
        "success" : function(d) {
            Ti.API.info("uploadPhoto: responseText is => " + d.responseText);
            Ti.API.info("uploadPhoto: metaDataText is => " + d.metaDataText);
        },
        "error" : function(d) {
            Ti.API.error("uploadPhoto: error is => " + d.errorText);
        }
    });


## Creating An Account with  Facebook

The external account login will create a Cocoafish account if it hasn't been created, otherwise, it will login using the user who has the matching external account info.

Here is where you call the cocoafish API; but as you can see it has the standard Appcelerator Facebook login code.
This is how you get the user's Facebook Access Token which is required to login with the account

We also have the actual function that call CocoaFish API to link the two accounts, that code in wrapped in the function `loginWithFacebook`

    Titanium.Facebook.appid = options.facebookAppId;
    Titanium.Facebook.permissions =  ['publish_stream'];
    Titanium.Facebook.addEventListener('login', function(e) {
        if(e.success) {
            loginWithFacebook();
        } else if(e.error) {
            alert(e.error);
        } else if(e.cancelled) {
            alert("Cancelled");
        }
    });

    if ( Titanium.Facebook.loggedIn === false ) {
        Titanium.Facebook.authorize();
    } else {
        loginWithFacebook();
    }

If it worked, you should see this when you look at the users account information in the console

    "external_accounts": [
         {
             "external_type": "facebook",
             "external_id": "10000xxxxxxxxxx",
             "token": "AAAxxxx9AnMkEBADsxxxxxxxxxxxxxxxxxDJAW73beQTXb6jIf8xxxxxIgZDZD"
         }
    ]

Inside of `loginWithFacebook`, we use the facebook uid to log in and/or create an account in CocoaFish.

    function loginWithFacebook() {
        var data = {
            type : "facebook",
            token : Titanium.Facebook.accessToken
        };

        cocoaFish.apiCall({
            "baseUrl" : "users/external_account_login.json",
            "httpMethod" : "POST",
            "params" :data,
            success : function(d) {
                Ti.API.info("responseText is => " + d.responseText);
                Ti.API.info("metaDataText is => " + d.metaDataText);
                var user = (JSON.parse(d.responseText)).response.users[0];
            },
            error : function(d) {
                Ti.API.error("error is => " + d.errorText);
            }
        });
    }


### Unlinking Account From Facebook

Unlinking the account from facebook is pretty straight forward. Just use the Facebook UID from the user and make the API call.

    function unlinkWithFacebook() {

        cocoaFish.apiCall({
            "baseUrl" : "users/external_account_unlink.json",
            "httpMethod" : "DELETE",
            "params" :{ 
                "type" : "facebook",
                "id" : Titanium.Facebook.uid,
            },
            success : function(d) {
                Ti.API.info("responseText is => " + d.responseText);
                Ti.API.info("metaDataText is => " + d.metaDataText);
            },
            error : function(d) {
                Ti.API.error("error is => " + d.errorText);
            }
        });            

    }
    
You might need to either login or ensure you have the `Titanium.Facebook.uid` or you could have saved it from the user's account.

All the external account information is accessible from the CocoaFish user object, see listing of external accounts above.


#### Creating an Account with Twitter

It is important to note that whatever twitter client you use, you will need to get the access_token and the users twitter id in order to make the API call to cocoafish

    function initWithTwitter() {
	var twitterFactory = require('twitter');
	var tt = new twitterFactory.twitter({
		service : 'twitter',
		consumer_key : "your_twitter_consumer_key",
		consumer_secret : "your_twitter_consumer_secret"
	});

	//
	// since we were successful, we can get the parameters needed to
	// make the cocoafish api call to login/create external account.
	//
	// these configuration values are available from a customized
	// version of the birdhouse.js twitter module
	//
	var authorizationSuccess = function() {
		var config, param_data;
		config = tt.getConfig();
		Ti.API.info("access_token is => " + config.access_token);
		Ti.API.info("user_id is => " + config.user_id);
		Ti.API.info("screen_name is => " + config.screen_name);

		//
		// pass the params in following the cocoafish documentation
		var data = {
			type : "twitter",
			token : config.access_token,
			id : config.user_id
		};

		//
		// make the API call and you are done.
		//
		// if the account exists already, then the user will be
		// logged in
		cocoaFish.apiCall({
			"baseUrl" : "users/external_account_login.json",
			"httpMethod" : "POST",
			"params" : data,
			success : function(d) {
				Ti.API.info("responseText is => " + d.responseText);
				Ti.API.info("metaDataText is => " + d.metaDataText);
				var user = (JSON.parse(d.responseText)).response.users[0];
				Ti.API.info("user is => " + user);
			},
			error : function(d) {
				Ti.API.error("error is => " + d.errorText);
			}
		})
	}
	//
	// call to authorize with twitter account information. On success an account
	// will be created for you. In the future you can login with the same twitter
	// credentials
	tt.authorize(authorizationSuccess);
    }

    
# [DOWNLOAD THE MODULE HERE](http://bit.ly/sYodsP)