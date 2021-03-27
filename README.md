# Web-Crawler

## High-Level Approach:
- Strategizing before starting:
  - For starters, we will be HTTP/1.0 to avoid the complication of having to add support chunked responses until we have gotten it to work with HTTP/1.0, then we will add support for HTTP/1.1.
  - We plan to handle aspects of HTTP as such:
    - HTTP GET - download HTML (possibly compress)
    - HTTP POST - For the initial login.
    - Cookie Management - we will grab the cookie when doing the initial HTTP POST and send HTTP GET requests with the cookie to avoid having to login multiple times.
  - Codes we intend to handle:
    - 301 - If this code is presented, we will add the new location to the queue and move on.
    - 403 and 404 - move on.
    - 500 - Re-add to the queue and try again in a little bit.
  - Functionality:
    - Track the frontier - as we find urls, instead of exploring them immediately, we will add them to a queue, which will be implemented by a hashmap to avoid duplicate entries.
    - Loops - as discussed in tracking the frontier, we will never visit a page twice.
    - Crawl the target domain - only add urls to the queue if they contain "fring.ccs.neu.edu" within them.
  - Logging in - Reverse engineer the html login form keeping CSRF in mind.
  - Throughout all of this we will scan every page for urls following the rules we described above and we will also be looking for keys that follow the style of class='secret_flag'
- Beginning to throw down some code:
  - Absolute first step was formatting the command line interactions.
  - We then started with the login method which proved to be far more difficult than expected. We figured out quickly that a GET request was required to get the csrf token, then we found there was two required. 
  - After two days of stuggling with the POST request, we got the cookie we were hungry for and a session id to begin collecting urls to inspect for flags.
- Page Parsing:
  - We first generalized the way we were sending GET requests.
  - Next, we implemented methods to extract urls from the html pages and organized them in a dict to store ones we've seen and an array to store ones we will be visiting next. We initially were thinking about using html.parse, but honestly it was more work than it was to write our own versions of those methods. We wrote a dynamic way of finiding pertinent urls and grabbed them in total regardless of the total size of the listed url.
  - Next we began to search for flags in all the pages we visited. 

## Challenges:
- First main challenge we face was getting our POST request formatted. This was partly a product of the project instructions saying we could use http 1.0, but we also did not format our initial GET request to get the two csrf tokens poorly. In addition to this, we needed to look at the developer tools in FireFox to get a good example of what a POST request for that site should look like.
- A challenge was how we were going to store and update urls to view. For instance, when running a while loop on an array, how we would update as we found new urls.
- Another challenge was the fact that the web server spontaneously starts to send nothing back and closes the connection. This was a major initial hurdle to get over.

## Testing:
- The initial primary way of testing was a lot of incremental print statements. Every time we thought something would work, we printed the outcome, specifically responses.
- We also tested the command line specifications to ensure we handled invalid inputs correctly.

