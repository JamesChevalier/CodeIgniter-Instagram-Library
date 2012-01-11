CodeIgniter Instagram Library
=============================

This library provides a CodeIgniter application access to the Instagram API.


Pre-requisites
--------------

* A functional CodeIgniter application
* A registered client with Instagram - this can be completed at http://instagram.com/developer/
	- Set the OAuth redirect_uri of your registered client to http://YOURDOMAIN/instagramredirect


Configuration for Development
-----------------------------

* Copy the files within the application/libraries folder to the application/libraries folder in your CodeIgniter application
* Copy the application/controllers/instagram.php file to the application/controllers folder in your CodeIgniter application
* Enter your registered client details, supplied by Instagram, into this library's application/config/config.php file
* Copy the contents of this library's application/config/config.php file into your CodeIgniter application's config.php file
	- If you'd prefer to keep a separate configuration file:
		* Rename this library's application/config/config.php file to Instagram_config.php
		* Copy the file into your CodeIgniter application's application/config directory
		* Load the configuration file in your Controller with the code `$this->config->load('Instagram_config');`
* Authorize your CodeIgniter application to use your Instagram account by using the provided Controller & entering the access token into the corresponding configuration variable
* Add the following code to your Controller to load the library, set the required variables, and set the access token:

	```php
	$this->load->library('instagram_api');
	$ig_client_id = $this->config->item('instagram_client_id');
	$ig_client_secret = $this->config->item('instagram_client_secret');
	$ig_redirect_uri = $this->config->item('instagram_redirect_uri');
	$this->instagram_api->access_token = $this->config->item('instagram_access_token');
	```

* Use the Function Reference section of this README (below) to make calls to the Instagram API


Notes
-----

At the moment, this only provides access to GET actions in the Instagram API.  I'm expecting POST and DELETE actions to be added soon.

The Controller provided in this library (and the explanation of Authorization, above) is only for authorizing your CodeIgniter application with your own account in order to allow easier development and testing.  You will need to create your own authorization methods for multi-user CodeIgniter applications.

This library uses Jamie Rumbelow's MY_input.php class to allow ? in CodeIgniter URLs. For more information visit http://www.jamierumbelow.net and see the MY_Input.php file included in application/libraries.


Function Reference
------------------

This section of the README is a working list: more functions/features will be added/updated as soon as possible.
A sample of the function results can be seen at http://ianluckraft.co.uk/demonstrations/instagram-library/welcome/

All Instagram API functions, apart from getPopularMedia(), require an access token which Instagram supplies through OAuth.  This requirement is handled by obtaining the access token, and then setting `$this->instagram_api->access_token` after loading this library.

In cases of the USERS endpoint of the Instagram API, where the passed variable is "$userid", you may substitute the string "self" to return the authenticated user's information.

You can use this general code format to make a function call and display it's relevant output:

	$igdata = $this->instagram_api->getUserFeed("self");
	$igres = $igdata->data;
	echo "<pre>";
	print_r($igres);
	echo "</pre>";


Library Specific
----------------

Produce the link required to authorize Instagram:
`$this->instagram_api->instagramLogin($ig_client_id, $ig_redirect_uri);`
* $ig_client_id is your client's client ID
* $if_redirect_uri is your client's redirect uri

Authorize to Instagram:
`$this->instagram_api->authorize($ig_client_id, $ig_client_secret, $ig_redirect_uri, $code);`
* $ig_client_id is your client's client ID
* $ig_client_secret is your client's secret
* $if_redirect_uri is your client's redirect uri
* $code is the code generated by Instagram when the user has been sent to your client's redirect url


No Authentication Necessary
---------------------------

Get the latest popular photos:
`$this->instagram_api->getPopularMedia($ig_client_id);`
* This function only requires your Instagram client id and no OAuth token


USERS
-----

Get basic information about a user:
`$this->instagram_api->getUser($user_id);`
* $user_id is either the ID of a user or "self" (with the quotes)

See the authenticated user's feed:
`$this->instagram_api->getUserFeed($max, $min, $count);`
* $max (optional) Return media earlier than this max_id
* $min (optional) Return media later than this min_id
* $count (optional) Count of media to return

Get the most recent media published by a user:
`$this->instagram_api->getUserRecent($user_id, $max_id, $min_id, $count, $max_timestamp, $min_timestamp);`
* $user_id is either the ID of a user or "self" (with the quotes)
* $max (optional) Return media earlier than this max_id
* $min (optional) Return media later than this min_id
* $count (optional) Count of media to return
* $max_timestamp (optional) Return media after this UNIX timestamp
* $min_timestamp (optional) Return media before this UNIX timestamp

See the authenticated user's list of media they've liked:
`$this->instagram_api->getUserLikes($user_id, $max_id, $count);`
* $user_id is either the ID of a user or "self" (with the quotes)
* $max_id (optional) Return media liked before this id
* $count (optional) Count of media to return
* This list is ordered by the order in which the user liked the media.
* Private media is returned as long as the authenticated user has permission to view that media.
* Liked media lists are only available for the currently authenticated user.

Search for a user by name:
`$this->instagram_api->userSearch($query, $count);`

* $query A query string
* $count (optional) Number of users to return


RELATIONSHIPS
-------------

* Required scope: relationships - Be able to follow and unfollow users on a user's behalf

Get the list of users this user follows:
`$this->instagram_api->userFollows($user_id);`

* $user_id is either the ID of a user or "self" (with the quotes)

Get the list of users this user is followed by:
`$this->instagram_api->userFollowedBy($user_id);`

* $user_id is either the ID of a user or "self" (with the quotes)

List the users who have requested this user's permission to follow:
`$this->instagram_api->userRequestedBy();`

Get information about the current user's relationship (follow/following/etc) to another user:
`$this->instagram_api->userRelationship($user_id);`
* $user_id is the ID of a user (must be user ID, cannot be "self")

Modify the relationship between the current user and the target user: (*Not implemented yet)
`$this->instagram_api->modifyUserRelationship($user_id, $action);`
* $user_id is the ID of a user (must be user ID, cannot be "self")
* $action One of follow/unfollow/block/unblock/approve/deny


MEDIA
-----

Get information about a media object:
`$this->instagram_api->getMedia($media_id);`
* $media_id is the ID of an image

Search for media in a given area:
`$this->instagram_api->mediaSearch($latitude, $longitude, $max_timestamp, $min_timestamp, $distance);`
* $latitude Latitude of the center search coordinate. If used, longitude is required.
* $longitude Longitude of the center search coordinate. If used, latitude is required.
* $max_timestamp A unix timestamp. All media returned will be taken earlier than this timestamp.
* $min_timestamp A unix timestamp. All media returned will be taken later than this timestamp.
* $distance Default is 1km (distance=1000), max distance is 5km

Get a list of what media is most popular at the moment:
`$this->instagram_api->popularMedia();`


COMMENTS
--------

* Required scope: comments - Be able to create or delete comments on a user's behalf

Get a full list of comments on a media:
`$this->instagram_api->mediaComments($media_id);`

* $media_id is the ID of an image

Create a comment on a media: (*Not implemented yet)
`$this->instagram_api->postMediaComment($media_id, $text);`

* $media_id is the ID of an image
* $text Text to post as a comment on the media as specified in media-id.

Remove a comment either on the authenticated user's media or authored by the authenticated user: (*Not implemented yet)
`$this->instagram_api->deleteMediaComment($media_id, $comment_id);`

* $media_id is the ID of an image
* $comment_id is the ID of a comment


LIKES
-----

* Required scope: likes - Be able to like and unlike items on a user's behalf

Get a list of users who have liked this media:
`$this->instagram_api->mediaLikes($media_id);`

* $media_id is the ID of an image

Set a like on this media by the currently authenticated user: (*Not implemented yet)
`$this->instagram_api->postLike($media_id);`

* $media_id is the ID of an image

Remove a like on this media by the currently authenticated user: (*Not implemented yet)
`$this->instagram_api->removeLike($media_id);`

* $media_id is the ID of an image


TAGS
----

Get information about a tag object:
`$this->instagram_api->getTags($tag);`

* $tag is the tag name

Get a list of recently tagged media:
`$this->instagram_api->tagsRecent($tag, $max_id, $min_id);`

* $tag is the tag name
* $max_id Return media after this max_id
* $min_id Return media before this min_id
* This media is ordered by when the media was tagged with this tag, rather than the order it was posted.
* Use the max_tag_id and min_tag_id parameters in the pagination response to paginate through these objects.

`$this->instagram_api->tagsSearch($query);`

* $query Valid tag name without a leading #. (eg. snow, nofilter)


LOCATION
--------

Get information about a location:
`$this->instagram_api->getLocation($location);`

* $location is the ID of the location

Get a list of recent media objects from a given location:
`$this->instagram_api->locationRecent($location, $max_id, $min_id, $max_timestamp, $min_timestamp);`

* $location is the ID of the location
* $max_id Return media after this max_id
* $min_id Return media before this min_id
* $max_timestamp Return media after this UNIX timestamp
* $min_timestamp Return media before this UNIX timestamp

`$this->instagram_api->locationSearch($latitude, $longitude, $foursquare_id, $foursquare_v2_id, $distance);`

* $latitude Latitude of the center search coordinate. If used, lng is required.
* $longitude Longitude of the center search coordinate. If used, lat is required.
* $foursquare_id Returns a location mapped off of a foursquare v1 api location id. If used, you are not required to use lat and lng. Note that this method is deprecated; you should use the new foursquare IDs with V2 of their API.
* $foursquare_v2_id Returns a location mapped off of a foursquare v2 api location id. If used, you are not required to use lat and lng.
* $distance Default is 1000m (distance=1000), max distance is 5000


GEOGRAPHIES
-----------

Get most recent media from a geography subscription that you created: (*Un-tested)
`$this->instagram_api->geographyEndpoints($media_id);`

* $media_id is the ID of an image
* You can only access Geographies that were explicitly created by your OAuth client