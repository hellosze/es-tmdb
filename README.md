Elasticsearch Index for the [The Movie Database](http://themoviedb.com).

# Install Elasticsearch w/ Dependencies

## Using Docker

### Build containers

With Docker installed, build the containers as below. You only need to do this once.

```
cd es-docker
docker build --tag=elasticsearch-tlre .
cd ../kb-docker
docker build --tag=kibana-tlre .
cd ..
```

### Run containers

```
docker-compose up
```

Browse to http://localhost:9200 and http://localhost:5601 to confirm ES / Kibana running



## Install Manually

### Elasticsearch

1. Download [Elasticsearch 7.5.1](https://www.elastic.co/downloads/past-releases/elasticsearch-7-5-1)
2. Unzip to where you'd like to run Elasticsearch
3. Add the following to config/elasticsearch.yml

```
http.cors.allow-origin: "/https?:\\/\\/(.*?\\.)?(quepid\\.com|splainer\\.io)/"
http.cors.enabled: true
indices.query.bool.max_clause_count: 10240
```

4. Install the Elasticsearch LTR plugin for 7.5.1:

```
bin/elasticsearch-plugin install -b http://es-learn-to-rank.labs.o19s.com/ltr-1.1.2-es7.5.1.zip
```

5. Run Elasticsearch

```
bin/elasticsearch
```

6. In your browser, navigate to [http://localhost:9200](http://localhost:9200) to confirm Elasticsearch is running

### Kibana

1. Download [Kibana 7.5.1](https://www.elastic.co/downloads/past-releases/kibana-7-5-1)

2. Unzip to where you'd like to run Kibana

3. Install the matching version of the [Kibana Analyze Plugin](https://github.com/johtani/analyze-api-ui-plugin)

```
bin/kibana-plugin install https://github.com/johtani/analyze-api-ui-plugin/releases/download/7.5.1/analyze_api_ui-7.5.1.zip
```

4. Run Kibana

```
bin/kibana
```

6. In your browser, navigate to [http://localhost:5601](http://localhost:5601) to confirm Kibana is running with the Analyze plugin included

# Index TMDB movies

Once installed, grab TMDB data and index into Elasticsearch

1. Download [tmdb.json](http://es-learn-to-rank.labs.o19s.com/tmdb.json), run `curl -o tmdb.json http://es-learn-to-rank.labs.o19s.com/tmdb.json`
2. Install [Python 3.6] or greater (https://www.python.org/downloads/)
3. Install [elasticsearch Python libraries](https://elasticsearch-py.readthedocs.io/en/master/) library, run `pip install elasticsearch`
  * Optional set-up a virtual ennvironment before install
    `python3 -m venv .`
    `source bin/activate`

4. Run `python3 indexTmdb.py` to index movies

# Confirm Elasticsearch has TMDB movies

Run a [wildcard search](http://localhost:9200/tmdb/_search?q=*) and confirm you get results.

# Postman

[Postman](https://www.postman.com/) is an API development tool, that helps build, run and manage API requests. The examples from the TLRE slides exist here too as a Postman Collection (`es-TLRE-postman_collection.json`). We like using Postman becasue it makes tinkering with query parameters nicer and we think it is a useful way to follow along as you learn about tuning search relevance.

If you want to use Postman during the TLRE class:

1. Download [Postman](https://www.postman.com/downloads/) for your OS
2. Open Postman and Import (top-menu >> File) `es-TLRE-postman_collection.json`
3. Define a global variable (grey eye icon in the upper-right) `es-host` to point to your running Elasticsearch instance (default is `localhost:9200`)
4. Tinker with the base URL, Params or JSON Body (optional)
5. Press 'Send' (blue rectangle button right of URL bar)

This collection is also valuable for testing examples against new versions of Elasticsearch. Using Postman's command line tool [Newman](https://github.com/postmanlabs/newman) you can check all of the requests in the collection:

```
newman run --global-var "es_host=localhost:9200" es-TLRE-postman_collection.json
```
