# images2iiif
This pipeline aims at converting static images scraped on a web site to the IIIF framework
(work initiated during a secondment at the UK national archives — [OHOS](https://ohos.ac.uk/our-project/) project)


## Scraping

The test bed: [People's Collection Wales](https://www.peoplescollection.wales/discover/query/Women%20for%20life%20on%20earth)
and [Women Archives Wales](https://www.peoplescollection.wales/user/3062/author/3062/sort/date/page/1)

[How-to](https://blog.finxter.com/a-complete-guide-to-set-up-splash-and-scrape-images-from-a-dynamic-website/)
with [Scrapy](https://scrapy.org/) + [Splash](https://splash.readthedocs.io/en/stable/)


a. Launch the Splash image:

> docker pull scrapinghub/splash

b. Run scrapinghub/splash in Docker (port 8050).
Test the service on [8050](http://localhost:8050/).

c. Install scrapy:

> pip install scrapy-splash

d. Create a scrapy project (e.g. PCW):

> mkdir PCW; cd PCW

> scrapy startproject PCW

d. Set up splash within scrapy:

Edit settings.py and add these parameters (port = 8050) :

```
SPLASH_URL = 'http://localhost:8050/'

DOWNLOADER_MIDDLEWARES = {'scrapy_splash.SplashCookiesMiddleware': 7,
                        'scrapy_splash.SplashMiddleware': 725,
                        'scrapy.downloadermiddlewares.httpcompression.HttpCompressionMiddleware': 810}

SPIDER_MIDDLEWARES = {'scrapy_splash.SplashDeduplicateArgsMiddleware': 100}

DUPEFILTER_CLASS = 'scrapy_splash.SplashAwareDupeFilter'

HTTPCACHE_STORAGE = 'scrapy_splash.SplashAwareFSCacheStorage'
```

Copy the Python spider into myproject/spiders (give a name to the spider, e.g. 'pagescrap' and set the top level URL to be scraped):
```
class pcwspider(scrapy.Spider):
  name = 'pagescrap'
  url = 'https://www.peoplescollection.wales/discover/query/Women%20for%20life%20on%20earth/'
  ...
```

Two spiders are involved:
1.  Scraping the pages of the results list for document URLs.
2.  Scraping each document page for metadata.

Notes :
- HTML elements are searched (div, img, p, alt, src...)
- no automatic pagination feature: range of pages (from the results list) must be set in the first spider
- metadata are generated as CSV files; textual descriptions as TXT files

e. Run the spiders:

> scrapy crawl pagescrap
> 
> scrapy crawl itemscrap

