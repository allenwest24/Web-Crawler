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
  - Throughout all of this we will scan every page for urls following the rules we described above and we will also be looking for keys that follow the style of <h2 class='secret_flag' style="color:red">FLAG: 64-characters-of-random-alphanumerics</h2>

## Challenges:



## Testing:


