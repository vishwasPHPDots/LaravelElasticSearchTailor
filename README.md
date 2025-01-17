# LaravelElasticSearchTailor
Data search via Elastic Search Package

# Installation

Install this package by this command : 
```bash
composer require phpdots/elasticsearchtailor
```

The service provider will be automatically registered using package discovery.

If you don't use auto-discovery you should add the service provider to the providers array in `config/app.php`.

```php
// existing providers...
PHPDots\ElasticSearchTailor\ElasticSearchTailorServiceProvider::class,
```

Once the package is installed you should publish the configuration.

```shell
php artisan vendor:publish --provider="PHPDots\ElasticSearchTailor\ElasticSearchTailorServiceProvider"
```

You can set your nodes in the config file `config\elasticcConfig.php`.

```php
<?php 
// Your nodes resides here.
return array(
    'hosts' => ['localhost:8000'],
);
?>
```

# Usage

Fist , you need to import our class in your controller or model. 

```php
use PHPDots\ElasticSearchTailor\Elastic;
```

Then you need to create an instance of the class. Like:

```php
$elastic = new Elastic();
```

Now, you are ready to use our functions.
To create a new index in elasrticseearch, we have a function `createIndex` . 

`createIndex($name='default', $shards=1, $replicas=1)`. 

It gives acknowledgement from the elasticsearch.

For example : 
```php
$result= $this->elastic->createIndex($index, 1, 1);
```

To put mappings into a  type/table in elasticsearch, there is a function `putSettings`. 

`putSettings($index='default', $mappings, $type)`. 

It also gives acknowledgement from elasticsearch. `$mappings` must be an array of valid settings.


For example :
```php
$result = $this->elastic->putSettings($index, $mapping, $type);
```

To index documents/records, we have a function `bulkIndex`. 

`bulkIndex($index, $indexType, $start_range)`.

`$index` is index name in which data is going to be indexed. `$indexType` is  `full` or `partial`. In full, all the records are going to be indexed.In full, previously indexed records will be updated. In partial, indexing will start from last indexed record. Only new records are going to be indexed here.


Right now, we have too much data to index, so we have decided not to index all the data in single function call, we can pass `$start_range` , 1,000,000 records after that will be indexed.

For example : 
```php
\\To index data after 9 million records.
$this->elastic->bulkIndex('index', 'full', 9000000);
```

For the Search, we have just implemented search on some fields which we can improve later. 

`search($index, $type, $start_date, $last_date, $mark_text='', $owner_name='', $exact=0)`. 

Here we have `$index`
 for index name, `$type` for the type we want to search in, `$start_date` to specify the from which date we want the records, similarly `$end_date` for the last date to which we want the records from, `$mark_text` and `$owner_name` for a specific field and `$exact` keep it 0 for fuzzy search and 1 for exact keyword search for `$mark_text`.
 
 
For example : 
```php
$result = $this->elastic->search('index_name', 'type_name', '27-05-2019', '01-06-2019', 'mark_text', 'owner_name');
```


This is a sample return object: 
```php
{
  "took" : 63,
  "timed_out" : false,
  "_shards" : {
    "total" : 5,
    "successful" : 5,
    "skipped" : 0,
    "failed" : 0
  },
  "hits" : {
    "total" : 1000,
    "max_score" : null,
    "hits" : [ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "0",
      "sort": [0],
      "_score" : null,
      "_source" : {"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "sort": [1],
      "_score" : null,
      "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, ...
    ]
  }
}
```

Our Search function will only return:  

```php
[ {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "0",
      "sort": [0],
      "_score" : null,
      "_source" : {"account_number":0,"balance":16623,"firstname":"Bradshaw","lastname":"Mckenzie","age":29,"gender":"F","address":"244 Columbus Place","employer":"Euron","email":"bradshawmckenzie@euron.com","city":"Hobucken","state":"CO"}
    }, {
      "_index" : "bank",
      "_type" : "account",
      "_id" : "1",
      "sort": [1],
      "_score" : null,
      "_source" : {"account_number":1,"balance":39225,"firstname":"Amber","lastname":"Duke","age":32,"gender":"M","address":"880 Holmes Lane","employer":"Pyrami","email":"amberduke@pyrami.com","city":"Brogan","state":"IL"}
    }, ...
    ]
```

If there is any error `search` function will return only `null array`.

We hope it helps you.


























