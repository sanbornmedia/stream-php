stream-php
==========

[![image](https://secure.travis-ci.org/GetStream/stream-php.png?branch=master)](http://travis-ci.org/GetStream/stream-php)

[![Coverage Status](https://coveralls.io/repos/GetStream/stream-php/badge.png?branch=master)](https://coveralls.io/r/GetStream/stream-php?branch=master)

### Installation

#### Install with Composer

If you're using [Composer](https://getcomposer.org/) to manage
dependencies, you can add Stream with it.

```javascript
{
    "require": {
        "get-stream/stream": "$VERSION"
    }
}
```

(replace `$VERSION` with one of the available versions on
[Packagist](https://packagist.org/packages/get-stream/stream)) or to get
the latest version off the master branch:

```javascript
{
    "require": {
        "get-stream/stream": "dev-master"
    }
}
```

Note that using unstable versions is not recommended and should be
avoided.

Composer will take care of the autoloading for you, so if you require
the `vendor/autoload.php`, you're good to go.

### Usage

```php
// Instantiate a new client
$client = new GetStream\Stream\Client('YOUR_API_KEY', 'API_KEY_SECRET');
// Find your API keys here https://getstream.io/dashboard/

// Instantiate a feed object
$user_feed_1 = $client->feed('user:1');

// Get 20 activities starting from activity with id $last_id (fast id offset pagination)
$results = $user_feed_1->getActivities(0, 20, $last_id);

// Get 10 activities starting from the 5th (slow offset pagination)
$results = $user_feed_1->getActivities(5, 10);

// Create a new activity
$data = [
    "actor"=>"1",
    "verb"=>"like",
    "object"=>"3",
    "foreign_id"=>"post:42"
];
$user_feed_1->addActivity($data);
// Create a bit more complex activity
$now = new \DateTime("now", new \DateTimeZone('Pacific/Nauru'));
$data = ['actor' => 1, 'verb' => 'run', 'object' => 1, 'foreign_id' => 'run:1', 
	'course' => ['name'=> 'Golden Gate park', 'distance'=> 10],
	'participants' => ['Thierry', 'Tommaso'],
	'started_at' => $now
];
$user_feed_1->addActivity($data);

// Remove an activity by its id
$user_feed_1->removeActivity("e561de8f-00f1-11e4-b400-0cc47a024be0");

// Remove activities by their foreign_id
$user_feed_1.remove('post:42', true)

// Follow another feed
$user_feed_1->followFeed('flat:42');

// Stop following another feed
$user_feed_1->unfollowFeed('flat:42');

// Batch adding activities
$activities = array(
    array('actor' => '1', 'verb' => 'tweet', 'object' => '1'),
    array('actor' => '2', 'verb' => 'like', 'object' => '3')
);
$user_feed_1->addActivities($activities);

// Add an activity and push it to other feeds too using the `to` field
$data = [
    "actor"=>"1",
    "verb"=>"like",
    "object"=>"3",
    "to"=>["user:44", "user:45"]
];
$user_feed_1->addActivity($data);

// Delete a feed (and its content)
$user_feed_1->delete();

// Generating tokens for client side usage
$token = $user_feed_1->getToken();

// Javascript client side feed initialization
// user1 = client.feed('user:1', "$token");

// Retrieve first 10 followers of a feed
$user_feed_1->followers(0, 10);

// Retrieve 2 to 10 followers
$user_feed_1->followers(2, 10);

// Retrieve 10 feeds followed by $user_feed_1
$user_feed_1->following(0, 10);

// Retrieve 10 feeds followed by $user_feed_1 starting from the 10th (2nd page)
$user_feed_1->following(10, 20);

// Check if $user_feed_1 follows specific feeds
$user_feed_1->following(0, 2, ['user:42', 'user:43']);

```

### Contributing

First, make sure you can run the test suite. Install development
dependencies :

    $ composer install

You may now use phpunit :

    $ vendor/bin/phpunit
