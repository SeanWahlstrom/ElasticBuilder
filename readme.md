# ElasticBuilder

_Query DSL Builder for Elasticsearch queries_

Use ElasticBuilder to easily combine multiple queries/filters/aggregations into Elasticsearch Query DSL.

## Usage 

ElasticBuilder is a series of Abstract classes you can use to map your query input arguments to the query DSL itself.

## Examples

**Extend Abstract Class**

First you simply extend one of the abstract query classes ... in this case Boolean

```php
use ElasticBuilder\Query\Boolean;

class ArticleQuery extends Boolean
{
 //do stuff
}
```

**Add Clause**

Here is how you go about adding a clause to a query (in this case must clause to bool query).

```php
if($this->request->has('search')){
    $search = $this->request->get('search');
    $match = \Eb::multi_match(['title^3','summary^1','body','userName^2','categoryName^2','tag_string^1'],$search,'and','cross_fields');
} else {
    $match = \Eb::match_all();
}
$this->must($match);
```

Here is an example of adding a filter to the bool query.

```php
$filter = \Eb::range('published_at',['lte' => Carbon::now()->toIso8601String()]);
$this->filter($filter);
```

Example of using a Facade

```php
$query = Eb::boolean()
    ->must(Eb::term('category_id',1))
    ->filter(Eb::range('published_at',['lte' => Carbon::now()->toIso8601String(),'gte' => Carbon::now()->subDay(10)->toIso8601String()]));
var_dump($query);
```

More Examples

```php
$query = \Eb::multi_match(['title^3','summary^1','body','userName^2','categoryName^2','tag_string^1'],'lorim ipsum','and','cross_fields');
var_dump($query);
```

```php
$query = Article::boolean()
    ->must(Eb::term('category_id',1))
    ->aggregate(Eb::agg()->terms('categories','category_id'));
var_dump($query);
```

```php
$query = Article::agg()
    ->terms('categories','category_id');
var_dump($query);

```