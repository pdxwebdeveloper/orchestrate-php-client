Orchestrate PHP Client
======================
A PHP client for [Orchestrate.io](http://orchestrate.io).

[![Build Status](https://travis-ci.org/SocalNick/orchestrate-php-client.png?branch=master)](https://travis-ci.org/SocalNick/orchestrate-php-client)

# Installation

```
$ composer require socalnick/orchestrate-php-client
```

# Creating a Client

```php
use SocalNick\Orchestrate\Client;
$client = new Client('your-api-key');
```

# Key / Value Operations

## Put (also used to create a collection)
```php
use SocalNick\Orchestrate\KvPutOperation;
$kvPutOp = new KvPutOperation("first_collection", "first_key", json_encode(array("name" => "Nick")));
$kvObject = $client->execute($kvPutOp);
$ref = $kvObject->getRef(); // 741357981fd7b5cb
```

## Put if-match
```php
use SocalNick\Orchestrate\KvPutOperation;
$kvPutOp = new KvPutOperation("first_collection", "second_key", json_encode(array("name" => "Terry")), array('if-match' => '741357981fd7b5cb'));
$kvObject = $client->execute($kvPutOp);
$ref = $kvObject->getRef(); // 0d1f15ab524a5c5a
```

## Put if-none-match
```php
use SocalNick\Orchestrate\KvPutOperation;
$kvPutOp = new KvPutOperation("first_collection", "second_key", json_encode(array("name" => "Bill")), array('if-none-match' => '*'));
$kvObject = $client->execute($kvPutOp); // null
```

## Get
```php
use SocalNick\Orchestrate\KvFetchOperation;
$kvFetchOp = new KvFetchOperation("films", "the_godfather");
$kvObject = $client->execute($kvFetchOp);
$ref = $kvObject->getRef(); // 9c1bc18e60d93848
```

## Get a previous version by ref
```php
use SocalNick\Orchestrate\KvFetchOperation;
$kvFetchOp = new KvFetchOperation("first_collection", "second_key", "741357981fd7b5cb");
$kvObject = $client->execute($kvFetchOp);
$ref = $kvObject->getRef(); // 741357981fd7b5cb
```

## Delete
```php
use SocalNick\Orchestrate\KvDeleteOperation;
$kvDeleteOp = new KvDeleteOperation("first_collection", "first_key");
$result = $client->execute($kvDeleteOp); // true
```

## Delete with purge
```php
use SocalNick\Orchestrate\KvDeleteOperation;
$kvDeleteOp = new KvDeleteOperation("first_collection", "first_key", true);
$result = $client->execute($kvDeleteOp); // true
```

## List
```php
use SocalNick\Orchestrate\KvListObject;
$kvListOp = new KvListOperation("films");
$kvListObject = $client->execute($kvListOp);
$count = $kvListObject->count(); // 10
$link = $kvListObject->getLink(); // /v0/films?limit=10&afterKey=the_godfather_part_2
```

## List with inclusive start key
```php
use SocalNick\Orchestrate\KvListObject;
$kvListOp = new KvListOperation("films", 5, 'anchorman');
$kvListObject = $client->execute($kvListOp);
$count = $kvListObject->count(); // 5
$link = $kvListObject->getLink(); // /v0/films?limit=5&afterKey=pulp_fiction
```

## List with exclusive after key
```php
use SocalNick\Orchestrate\KvListObject;
$kvListOp = new KvListOperation("films", 5, null, 'anchorman');
$kvListObject = $client->execute($kvListOp);
$count = $kvListObject->count(); // 5
$link = $kvListObject->getLink(); // /v0/films?limit=5&afterKey=shawshank_redemption
```

# Search

## Default Search
```php
use SocalNick\Orchestrate\SearchOperation;
$searchOp = new SearchOperation("films");
$searchResult = $client->execute($searchOp);
$count = $searchResult->count(); // 10
$total = $searchResult->totalCount(); // 12
```

## Search with query, limit, and offset
```php
use SocalNick\Orchestrate\SearchOperation;
$searchOp = new SearchOperation("films", "Genre:*Crime*", 2, 2);
$searchResult = $client->execute($searchOp);
$count = $searchResult->count(); // 2
$total = $searchResult->totalCount(); // 8
```
