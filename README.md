### Scrappy 

1. What is scrappy ?

- Scrapy is a Python framework specifically designed for web scraping.
- Using Scrapy you can easily build highly scalable scrapers that will retrieve a pages HTML, parse and process the data, and store it the file format and location of your choice.
  
2. why scrappy and when to use ? 
   
   - Scrapy is a Python framework designed specifically for web scraping. Built using Twisted, an event-driven networking engine, Scrapy uses an asynchronous architecture to crawl & scrape websites at scale fast.

    - With Scrapy you write Spiders to retrieve HTML pages from websites and scrape the data you want, clean and validate it, and store it in the data format you want.
  - Although, there are other Python libraries also used for web scraping:

    - Python Requests/BeautifulSoup: Good for small scale web scraping where the data is  - returned in the HTML response. Would need to build your own spider management    - functionality to manage concurrency, retries, data cleaning, data storage.
    - 
    - Python Request-HTML: Combining Python requests with a parsing library, Request-HTML     - is a middle-ground between the Python Requests/BeautifulSoup combo and Scrapy.
    - 
    - Python Selenium: Use if you are scraping a site if it only returns the target data  - after the Javascript has rendered, or you need to interact with page elements to     - get the data.
  
    - Python Scrapy has lots more functionality and is great for large scale scraping     - right out of the box:
    - 
    - CSS Selector & XPath Expressions Parsing
    - Data formatting (CSV, JSON, XML) and Storage (FTP, S3, local filesystem)
    - Robust Encoding Support
    - Concurrency Managament (if we scrape 1 page or 1000s of pages at the exact same time with the same piece of code)
    - Automatic Retries (if we try for a page and it comes back with error, it wil auto retry, we don't ve to worry about all the logic that goes into the auto retrace)
    - Cookies and Session Handling
    - Crawl Spiders & In-Built Pagination Support
    - You just need to customize it in your settings file or add in one of the many   
    - Scrapy extensions and middlewares that developers have open sourced.
    - 
    - The learning curve is initially steeper than using the Python Requests/ - BeautifulSoup combo, however, it will save you a lot of time in the long run when    
   - deploying production scrapers and scraping at scale.
   - ```pip install scrapy```
  
  # part 2 (project setup)

  - creating a project ```scrapy startproject bookscrpper```
  - the pipelines are what happens when we retuning/yielding the data from our spider (such as put the data into the items and then items into our db)
in the middlewares.py we code like how our spiders want to be operated(it gives us the control over lotta diff things such as timing a req how long the req wants to be go on for, what header we wanna make in our request, or multiple retries etc)
  - settings.py - how our spider and the middlewares are operating and whether disabled or enabled and other settings related things.
  
# Build the spider

- lets build our first spider ```scrapy genspider bookspider books.scrape.com``` the url of the website that we're going to scrape.(the practice site to scrape)
- will create a bookspider.py inside the spiders
- ```pip install ipython``` and make our scrapy shell as the ipython (in the config file)
- then in spiders dir run ```scrapy shell``` opens the ipython shell 
- ```fetch('url')``` will fetch the html from the url and put in the var (called response) and we can access it
- then ```response.css('article.product_pod')``` to get the product_pod css class in the article tag 
- to grab the first books - ```books = response.css('article.product_pod')```
- len(books) -- to find the len of the book ```book = books[0]```
- to get the title of the book that is in h3 and <a> tag ```book.css('h3 a::text').get()``` pulls the text 
- to get the url of the book which is in the href attribute of the <a> tag ```book.css('h3 a').attrib['href']```
- we can impl this in our parse fn to grab the title, price and the url of all the books
- and to run our spider ```scrapy crawl bookspider```
- now lets scrape the books in the next page and until there are no more books to scrape.

# part 5 

- crawl through the pages 
- using css selectors and xpath to extract more complex data
    - sometimes its easier to use the xpath than the css selectors, ```ex. response.xpath("//ul[@class='breadcrumb']/li[@class='active']/preceding-sibling::li[1]/a/text()").get```
    - we can use the preceeding-sibling or following-sibling to grab the item from the prev or next sibling in case of there is no class name or id for the target item.
- saving the data to csv or json.

  1. extract the data from the table.
      - ```table = response.css("table tr")``` all the table rows within the table.
      -  ```table[1].css.("td::text").get()``` to get the 2nd rows and the table data (tex)
   - and to save the data just add the output tag and the file name ```scrapy crawl bookspider -O bookdata.json```


# part 6 

- what are scrapy items
- using the scrapy items to structure our data
- what are scrapy pipelines
- cleaning our data with item pipelines

1. Items 
    - when we generate our project, we will ve the items.py file this is where we put our items.
    - the items helps us to define what we want in the block of data that we re scraping and returning
    - for ex- the book item(or obj), that we scraped has all the fields of in our spider, in  the item we can define the book item and assign the fields, now we ve our book item (obj)
    - 
  2. Pipelines
    - using the pipeline we can sanitize, format (str, or int etc), modify, validate our data 
    - and with pipeline we can also store our data instead of saving it json or csv we can directly save em on the database.
    - "Item Adapter" package is useful for handling different item types in a single interface.
    - with this adapter we can inject our item and loop thru all the fields.
    - once we done with the logic in our pipelines, then we ve to enable the pipeline in our settings.py

# part 7 

- saving the data to files and database.
  - saving data via command line
  - saving data via Feed setting (add a feed section in the settings.py)
  - saving data into database.
- if we wana apend the data to the existing file we can use the -o(insted of the O tag) flag ```scrapy crawl bookspider -o bookdata.json``` this will apend to the previous file instead of overwriting. and the capital O flag will overide em.
- once we add the feed section in the settings then the spider will crawl the data into the default file format that we mentioned in the settings. so therefore no need to add any output flag or even the file extn. ex ```scrapy crawl bookspider``will generate a databinding.json file (that we mentioned in the feed)
- we can also specify the feeds settings in the spider.py
- and we can create a database ```create database books```
- and to connect the scrapy to our database we ve to use the package ```pip install mysql mysql-connector-python```
- run the scrapy, and the data will be stored in the database.
- once its stored we can confirm by executing the following commands in sql shell >use books, show tables, select * from books, drop table books,

# Part 8 

- "Fake User Agent and Browser Headers"
 1. why we get blocked when scrapping 
 2. explaining and using the user agent to bypass the blocked
 3. explaining and using request header to bypass getting blocked

- the user agent is one of the field in the request header(we can see in the consolee under n/w)
- this user userAgent : Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/113.0.0.0 Safari/537.36
- we can use the "user agent.string.com" to analyze our user agent (we can see all the informations about our host, os browser etc)
- this userAgent are ok when scraping a small web pages, but if we wana scrap a large websites like amazon, google etc we will get blocked. there will be some sort of bots to stop.
- so they normally look for the ip, cookies, session, headers and they will set a flag so next time we go to the site they will know us. 
- so if we change the user agent every time we make a request, they will think that the request from the different browsers and let the request through.
- however for the most complicated web sites they want everything in the headers to be different every time, atleast slightly different.
- even if they don't block us thy will throw the captcha page to stop us
- so for that we need to change the entirety of the request headers.

- we can add the user agent in our settings.py or 
- we can add the list of user agents and rotate the list every time we make the request

# masive fake user agents

 - lets see how we make make thousands of fake users agents and we can loop through them, 
 - so instead of implementing in the bookscrapper.py, we can iimpl the logic in the middleware and rotate/loop through all the different fake users agents.
 - "scrapeops.io" - we can sign up to get the api for the fake header generator.
 - every sites has the robot.txt file the spider will automatically look for. for normal sites we can set it true in the settings.py, but for the complex sitess we ve to set it false in the settings.py -- ROBOT_OBEY = False
 - this file contains details about what infos/pages to be scraped or not to be.
  
# Rotating Proxy and proxy apis

- -What Are Proxies & Why Do We Need Them?
- The 3 Most Popular Proxy Integration Methods
- How To Integrate & Rotate Proxy Lists
- How To Use Rotating/Backconnect Proxies
- How To Use Proxy APIs


- earlier we ve changed the req header, but the ip addr that's the unique identifier 
- if we change our user agent every time and still hit the site with the same ip, then the site will likely to know that we re scrping.
- so for the complex sites we also need to rotate our ip addr and that's where the proxies comes in.
- there are diff methods we can implement the proxies.
1. genode free proxy list.com  2. free proxy list.com
    - these free sites are very slow coz of high throughput
    - or else they are already blacklisted, as some one used the ip already
    - we can install the package "scrapy-rotating-proxies" ```pip install scrapy-rotating-proxies```
    - add that in our settings file ROTATING_PROXY_LIST[]
    - or if we wana a add a file with the proxy list ROTATING_PROXY_LIST_FILE_PATH = "/filepaht/"
    - and then enable the middleware in settings.py DOWNLOADER_MIDDLEWARE

2. the anohter way of getting proxy is the proxy port, the service provided by the proxy provider 
     - the smartproxy.com
     - and set this in the bookspider as our res option parameter -- meta={"proxy": "http://username:password@gate.smartproxy.com:7000"})
     - then impl a myproxymw in the middlewware 
     - Then you just need to enable it in your settings.py file, and fill in your proxy connection details: -  'scrapy.downloadermiddlewares.httpproxy.HttpProxyMiddleware': 400, 

3. proxy api endpoints
      - when we don't ve to deal with the headers of the user agents, may be we re scraping something like the headless browsers to do the js running for us
      - we can get that by using the proxy endpt
      - the endpt service will then make sure that our reqs (certain things are enablled )and heads us to get the page data.
      - in the scrapeops in the proxy aggregator section we can grab the endpt in the request builder
      - And use this function in our Scrapy request:
      - yield scrapy.Request(url=get_proxy_url(url), callback=self.parse)
      - also instead of this doing directly in the spiders we can use imp in the middleware.
      - "`using Proxy Middleware"
      - The better approach to integrating proxy APIs into Scrapy spiders is by using a proxy middleware as there can be issues following URLs unless you customize the code to account for the Proxy URL.
      - ```pip install scrapeops-scrapy-proxy-sdk```
      - And then enable it in your project in the settings.py file.
      - there are many proxy provider settings we can mention in our settings.py file, like country, or the page to be js rendered and many other parameters etc.


# Part 10 

 - how you can deploy and run your spiders in the cloud with Scrapyd.

There are several ways to run and deploy your scrapers to the cloud which we will cover in this course:

- Scrapyd (free open source)
- ScrapeOps SSH Server Integration (free open source)
- Scrapy Cloud (paid)
  
  - Scrapyd is a great option for developers who want an easy way to manage production Scrapy spiders that run on a remote server.

  - With Scrapyd you can manage multiple servers from one central point by using a ready-made Scrapyd management tool like ScrapeOps, an open source alternative or by building your own.

- set up ```pip install scrapyd``` then run the ```scrapyd```
- and we can deply the scrapyd in any of the VMs provider such as vultr, DigitalOcean etc.
- and install all the required packages in the vm and also the requirements.txt ```pip install -r requirements.txt```
- make sure to enable the local host in the scrapy.cfg file ```vm /scrapy.cfg```
- and deploy it ```scrapyd-deploy project-name```
- and to run```curl http://localhost:6800/schedule.json -d project-default -d spider=bookspider```
- the scheduler we can assign in the scrapyd ui, once we assign the time it will generate the curl command in the ui. 
- and for the db we need some packages 1. flask-sql alchemy, 2. sql-alchemy 3.flask version and 4. werkzeug
- to stop the scrapyd ```kill processId``` kill followed by the process id
- and to start that again we ve to redeploy the server.

# Run spiders in cloud with the scrapyd

- ```pip install scrapeops-scrapy```
- grab the server id and put that in the scrapeops under the add server section - add new scrapyd server
- and we can also shcedule our spider in the scrapeops dashboard

# part 11 Run spiders in cloud with scrapeops

- What Are The ScrapeOps Job Scheduler & Monitor?
- rapeOps Scrapy Monitor
- tting Up The ScrapeOps Scrapy Monitor
- rapeOps Server Manager & Scheduler
- nnecting ScrapeOps To Your Server
- ploying Code From Github To Server With ScrapeOps
- heduling & Running Spiders In Cloud With ScrapeOps

- we can again use the vms to set our server
- and then in the scrapeops under add server section, this time instead of adding the scrapyd server we ve to choose the option to add the Server/vm
- add the server endpoint and grab the curl command.
- run that in our server once the server is provisioned 
- in the scrapeops dashboard we ve option to clone the repo, schedule the job, add spider etc.
- we can select the clone repo option and select our git project repo, and grab the deploy key ssh key.
- and ve to add the installation script to install the python, venv and the scrapy and the requirements.txt.
- and then put that in our github under settings deploy keys


# part 12 Run spiders in cloud with the scrapy cloud.

lets see how to deploy, schedule and run your spiders on any server with Scrapy Cloud.

- What Is Scrapy Cloud?
- Get Started With Scrapy Cloud
- Deploy Your Spiders To Scrapy Cloud From Command Line
- Deploy Your Spiders To Scrapy Cloud via GitHub
- Run Spiders On Scrapy Cloud
- Schedule Jobs on Scrapy Cloud

- Scrapy Cloud is a great spider hosting solution if you are building your scrapers using the Scrapy, and don't want to deal with setting up your own servers and job scheduling system.

- Scrapy Cloud boasts some pretty powerful features:

On-demand scaling
Easy integration with other Scrapy & Zyte products (Splash, Spidermon, Zyte Smart Proxy Manager)
Full suite of logging & data QA tools
- for that create an account in the zyte.scrapy cloud.com and follow the instructions. and deplpy the shub (scrapy hub)
- and clone our repository and run the spider

# part 14 

- for the advance part, we can do following steps.
- how to scrap the dynamic sites  (for that case we ve to use scrapy pupeteer or scrapy selenium to render the website in a headless browser)
- scraping behind the logins
- scaling scraping spiders with the worker architecture
- scrapy redis (if we re scraping tons of pages, to store all the urls in one central place and and the urls are pulling off by multiple diff server, and then all the urls are shared by multiple diff workers at the same time) is a distributed worker architecture.





