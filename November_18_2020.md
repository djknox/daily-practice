## Daily Practice
### Wednesday, November 18th, 2020
---


# [Learn Laravel Scout](https://laracasts.com/series/learn-laravel-scout)


## [Installation and Usage](https://laracasts.com/series/learn-laravel-scout/episodes/1)

Scout is a driver-based solution for adding full-text search to Eloquent models.  

Install via composer:  
- `composer require laravel/scout`  

Publish the `scout.php` configuration file:  
- `php artisan vendor:publish --provider="Laravel\Scout\ScoutServiceProvider"`

Add `Laravel\Scout\Searchable` trait to any model that you want to be searchable:  
Example:  
```
use Laravel\Scout\Searchable;

class User extends Authenticatable
{
    use Searchable;
}
```

Install the Algolia PHP SDK via composer:  
- `composer require algolia/algoliasearch-client-php`


Register at Algolia to get API key

Add the Algolia app id + secret as environment variables  
Example:  
`.env`  
```
ALGOLIA_APP_ID=my_app_id
ALGOLIA_SECRET=my_secret
```

These environment variables are used for the `id` and `secret` fields within the `algolia` array in `config/scout.php`  
Example:  
`config/scout.php`  
```
'algolia' => [
    'id' => env('ALGOLIA_APP_ID', ''),
    'secret' => env('ALGOLIA_SECRET', ''),
],
```

To change the database table that Scout uses, add a `searchableAs()` method that returns the table's name  
Example:  
```
use Laravel\Scout\Searchable;

class Post extends Model
{
    use Searchable;

    public function searchableAs()
    {
        return 'posts_index';
    }
}
```

By default, a model's entire `toArray()` result is sent to Algolia. To configure what is sent, add a `toSearchableArray()` method that returns the desired array  
Example:  
```
use Laravel\Scout\Searchable;

class Post extends Model
{
    use Searchable;

    public function toSearchableArray()
    {
        $array = $this->toArray();

        // Customize array...

        return $array;
    }
}
```

Import existing records into Algolia:
- `php artisan scout:import "App\Models\TheModelToImport"`

Remove records from Algolia:
- `php artisan scout:flush "App\Models\TheModelToRemove"`

Use the `search()` method to search models for a given string  
- `$orders = App\Models\Order::search('The string to search for')->get();`

Limit the attributes that will be searched by adding them to "Searchable attributes" in the Algolia index
- Configuration -> Searchable attributes



## [Searching on the Front-End](https://laracasts.com/series/learn-laravel-scout/episodes/2)

This lesson uses [`InstantSearch.js`](https://www.algolia.com/doc/guides/building-search-ui/what-is-instantsearch/js/), which is a vanilla JavaScript library for creating UIs with Algolia  

Use the `create-instantsearch-app` tool to bootstrap an `InstantSearch.js` app:
```
npx create-instantsearch-app ais-ecommerce-demo-app \
  --template "InstantSearch.js" \
  --app-id "my_app_id" \
  --api-key "my_public_api_key" \
  --index-name my_algolia_index_name \
  --attributes-to-display name
```

Start the app with `npm run start`  



## [Using Meilisearch](https://laracasts.com/series/learn-laravel-scout/episodes/3)

`MeiliSearch` is an open-source alternative to Algolia  

Download the latest release of `MeiliSearch` using Homebrew:  
- `brew update && brew install meilisearch`

Run the `MeiliSearch` server locally: `meilisearch`  

Install [Laravel Scout driver](https://github.com/meilisearch/meilisearch-laravel-scout) via composer:
- `composer require meilisearch/meilisearch-laravel-scout`

Update `.env` with new environment variables:  
```
SCOUT_DRIVER=meilisearch
MEILISEARCH_HOST=http://127.0.0.1:7700
MEILISEARCH_KEY=masterKey
```

[`MeiliSearch JavaScript`](https://github.com/meilisearch/meilisearch-js) is a JS library for creating search UIs with `MeiliSearch`  
- `npm install meilisearch-js`
