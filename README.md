# NewsCrawler
News Crawler example

Try to create a seperate virtual environment

  $ pip install virtualenv         # look for documentation on setting up virtual environment
  $ pip install virtualenvwrapper  # setup the PATH variable

  # open ~/.bashrc or ~/.profile or ~/.bash_profile and add this
  export WORKON_HOME=$HOME/.virtualenvs
  export VIRTUALENVWRAPPER_VIRTUALENV_ARGS='--no-site-packages'
  export PIP_VIRTUALENV_BASE=$WORKON_HOME
  source /usr/local/bin/virtualenvwrapper.sh # this might be different of other OS

  # now mkvirtualenv command will work and then type
  $ mkvirtualenv crawler
  (crawler) $ pip install -r requirements.txt

Setup Process

Create a JSON file in sources folder with required fields. These are generally rules for extracting link from the sites. you can refer more on scrapy docs. For example you can Refer to sample.json which has seekingalpha configuration for INTUIT company in source folder.
For start_urls enter the urls of the pages from the site where you find the listing of the articles as links.
To add the AJAX pages (or hidden listing) you have to enter it in the start_urls. You can also add query for these.
Rules will define which pages to parse from, in other words, it will match the urls with the expressions before proceeding. If it matches it will accept the link for either parsing, following or both.
Paths define the XPATHs to the different fields of the pages to the parsed. As in sample.json, we have paths to Title, Date, Text.


Sample Rule in JSON

Sample rule file for several websites for crawling has been provided in the source folder.
Also look for scrapy docs for writing rules for link extractions to get more clear picture of what is happening here.

{
  "allowed_domains" : ["seekingalpha.com"],
  "start_urls": [
    "http://seekingalpha.com/symbol/intu",
    "http://seekingalpha.com/account/ajax_headlines_content?type=in_focus_articles&page=1&slugs=intu&is_symbol_page=true"
  ],
  "rules": [
    {
      "allow": ["/symbol/intu"],
      "follow": true
    },
    {
      "allow": ["/account/ajax_headlines_content.*page=\\d+.*"],
      "follow": true
    },
    {
      "allow": ["/news-article.*intuit.*", "/article.*intuit.*"],
      "follow": true,
      "use_content": true
    },
    {
      "allow": ["/symbol/intu.*"],
      "deny": ["/author.*", "/user.*", "/currents.*", "/instablog.*"],
      "restrict_xpaths": ["//div[@id='main_container']", "//div[@class='symbol_articles_list mini_category']"],
      "follow": false,
      "use_content": true
    }
  ],
  "paths": {
    "title" : ["//title/text()"],
        "date" : ["//div[@class='datestamp']/text()", "//div[@class='article_info_pos']/span/text()"],
        "text" : ["//div[@id='article_content']", "//div[@id='article_body']"]
  },
  "source": "Seeking Alpha",
    "company": "Intuit"
}



Running

Once the json file for source setting is done, run the following in the terminal:

$ scrapy crawl NewsSpider -a src_json=sources/sample.json
