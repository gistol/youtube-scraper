# Youtube Scraper
Using Google API, Symfony 4 and Vuetify

## Prerequisites
PHP 7.1, node, npm

### Installing
```
git clone https://github.com/valerijv/youtube-scraper.git
cd youtube-scraper
composer install
npm install
```
edit .env file (set DATABASE_URL and GOOGLE_API_KEY) and run:
```
php bin/console doctrine:database:create
php bin/console doctrine:migrations:migrate
```
build frontend:
```
./node_modules/.bin/encore production
```

Set your web server to point to folder, otherwise it won't work:
```
youtube-scraper/public
```
Or if you really want to use a folder, you will have to add a path in webpack.config.js 
```
.setPublicPath('/youtube-scraper/public/build')
```

### Scraping
To scrape a channel use following command and specify a Youtube Channel ID. 
This command will fetch all videos or fetch only new videos if a channel already exist in your database. 
```
php bin/console app:get-videos UCnciA_RZVjq9DMvx1KB625Q
```
![Scraping videos](./readme/get-videos.png)

To scrape only statistics (views, likes, dislikes, comments and favorites) use following command:
```
php bin/console app:get-stats UCnciA_RZVjq9DMvx1KB625Q
```
![Scraping stats](./readme/get-stats.png)

Each scraper request gets you 50 Videos.  
Google API read quota limits are around 50,000 requests per project per day.  
Which will let you scrape about 1.25 million new youtube videos per day.  
Or will update statistics for 2.5 million existing videos.  
This numbers can be increased, by creating multiple google projects.  

### Scaling
You can easily run several scrapers in parallel. 
You might want to create a scheduler script which will run scraping commands with different channel IDs.  

##### Redis
Redis can be used for better performance in tags autocomplete.  
It also can be used to cache latest statistics.   
I would not recommend storing all statistics in Redis, because it can use too much resources.

##### Elasticsearch for statistics
Elasticsearch is a great candidate for storing statistics. 
It scales well and it has great aggregation queries like range and date histogram aggregations. 

##### Varnish cache
Finally, you might want to consider using Varnish Cache for caching static HTML pages with your statistics.  
This is great, because it will serve HTML directly to a user bypassing web server, php and a database.
