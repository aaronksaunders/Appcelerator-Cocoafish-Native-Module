![alt text](http://clearlyinnovative.com/images/logo_transparent.png "Title")

# Cocoa Fish Native iOS Module

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

    var cocoaFish = cocoafishMgr.create({
	    "consumer_key" : "dIUKXXXXXXXXXXXXXXXXXXXXXXXBb4",
	    "secret_key" : "kN0lemXXXXXXXXXXXXXXXXXXXXXXXPva"
    });


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

# [DOWNLOAD THE MODULE HERE](http://bit.ly/sYodsP)