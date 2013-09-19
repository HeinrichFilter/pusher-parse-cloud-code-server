# Pusher Server library for Parse Cloud code

This is a [Parse Cloud Code](https://www.parse.com/docs/cloud_code_guide) library for interacting with the Pusher REST API.

Registering at <http://pusher.com> and use the application credentials within your app as shown below.

This code is based on [pusher / pusher-node-server](https://github.com/pusher/pusher-node-server) which unfortunately depends on libraries that are incompatible with Parse Cloud Code.
All incompatible libraries calls were replaced with alternatives found from the web (see Credits)

Note: This new library has not been extensively tested yet. It works for my use case of triggering an event on a private channel.
Please let me know if it is not working as expected or if anything can be improved.

## Installation

After you created your Parse Cloud Code structure copy all the js files from this repo into the `cloud` directory.

## How to use

### Constructor

```javascript
var Pusher = require('cloud/pusher.js');

var pusher = new Pusher({
  appId: 'YOUR_PUSHER_APP_ID',
  key: 'YOUR_PUSHER_APP_KEY',
  secret: 'YOUR_PUSHER_SECRET_KEY'
});
```

### Publishing/Triggering events

To trigger an event on one or more channels use the trigger function.

#### A single channel

```
pusher.trigger( 'channel-1', 'test_event', { message: "hello world" } );
```

#### Multiple channels

```
pusher.trigger( [ 'channel-1', 'channel-2' ], 'test_event', { message: "hello world" } );
```

### Excluding event recipients

In order to avoid the person that triggered the event also receiving it the `trigger` function can take an optional `socketId` parameter. For more infromation see: <http://pusher.com/docs/publisher_api_guide/publisher_excluding_recipients>.

```
var socketId = '1302.1081607';

pusher.trigger(channel, event, data, socketId);
```

### Authenticating Private channels

To authorise your users to access private channels on Pusher, you can use the `auth` function:

```
var auth = pusher.auth( socketId, channel );
```

For more information see: <http://pusher.com/docs/authenticating_users>

### Authenticating Presence channels

Using presence channels is similar to private channels, but you can specify extra data to identify that particular user:

```
var channelData = {
	user_id: 'unique_user_id',
	user_info: {
	  name: 'Phil Leggetter'
	  twitter_id: '@leggetter'
	}
};
var auth = pusher.auth( socketId, channel, channelData );
```

The `auth` is then returned to the caller as JSON.

For more information see: <http://pusher.com/docs/authenticating_users>

### Application State

It's possible to query the state of the application using the `pusher.get` function.

    pusher.get( { path: path, params: params }, callback );

The `path` property identifies the resource that the request should be made to and the `params` property should be a map of additional querystring key and value pairs.

The following example provides the signature of the callback and an example of parsing the result:

		pusher.get( { path: '/channels', params: {} }, function( error, request, response ) {
			if( response.status === 200 ) {
				var result = JSON.parse( response.text );
				var channelsInfo = result.channels;
			}
		} );    

#### Get list of channels in an application

    pusher.get( { path: '/channels', params: params }, callback );

Information on the optional `params` option property and the structure of the returned JSON is defined in the [REST API reference](http://pusher.com/docs/rest_api#method-get-channels).

#### Get single channel state

    pusher.get( { path: '/channels/[channel_name]', params: params }, callback );

Information on the optional `params` option property and the structure of the returned JSON is defined in the [REST API reference](http://pusher.com/docs/rest_api#method-get-channel).

#### Get list of users on a presence channel

    pusher.get( { path: '/channels/[channel_name]/users' }, callback );

The `channel_name` in the path must be a [presence channel](http://pusher.com/docs/presence). The structure of the returned JSON is defined in the [REST API reference](http://pusher.com/docs/rest_api#method-get-users).

## Credits

This library is based on:
* <https://github.com/pusher/pusher-node-server>

The node.js crypto package was replaced with the following two libraries which were slightly modified to use the CommonJS export style:
* <http://point-at-infinity.org/jssha256/>
* <http://www.myersdaily.org/joseph/javascript/md5.js>

Node.js requests library calls were replaced with:
* [Parse.Cloud.httpRequest](https://www.parse.com/docs/cloud_code_guide#networking)

## License

This code is free to use under the terms of the MIT license unless the license in one of the third-party files states differently.
