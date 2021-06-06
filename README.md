# Web-Crawler

## Objective:
- Starting with 1 known social media account homepage, find urls for the remaining ~15,000 accounts and find 5 hidden flags somewhere throughout the social network.
  - Goal 1: Correctness - Find all 5 flags.
  - Goal 2: Effiency - Find all 5 flags and report back as soon as possible.
  - Goal 3: Resiliancy - Adapt to low network performance and chunking caused by HTTP 1.1 GET requests.

## Efficiency Benefit of this Webcrawler:
The naive approach for solving the efficiency problem witha  webcrawler would be to keep track of everything we see so that as we encoutner new pages we could see if it was already
in the table of urls to parse. We implemented this in a way where the search for having already seen a url and selecting a new one to parse could both be done in constant time. By using
an array for the queue of urls to parse next and then using a hashmap to check if we've seen and parsed through correctly, we could ensure full coverage while also not revisiting pages.
This really speaks to how the use of datastructures could save a ton of time if used correctly (sometiems at the cost of storage space).

## High-Level Approach:
- Strategizing before starting:
  - For starters, we tried using HTTP/1.0 to avoid the complication of having to add support chunked responses until we have gotten it to work with HTTP/1.0, then we intended to add support for HTTP/1.1. This did not work because the server only worked with 1.1.
  - We planned to handle aspects of HTTP as such:
    - HTTP GET - Request HTML
    - HTTP POST - For the initial login.
    - Cookie Management - Grab the cookie when doing the initial HTTP POST and send HTTP GET requests with the cookie to avoid having to login multiple times.
  - Codes to handle:
    - 301 - If this code is presented, we will add the new location to the queue and move on.
    - 403 and 404 - move on.
    - 500 - Re-add to the queue and try again in a little bit.
    - 400 - Stop the connection, pause, reconnect, retry request.
  - Functionality:
    - Track the frontier - As we find urls, instead of exploring them immediately, we will add them to a queue, which will be implemented as an array alongside a hashmap to avoid duplicate entries.
    - Loops - As discussed in tracking the frontier, we will never visit a page twice.
    - Crawl the target domain - Only add urls to the queue if they are part of our given scope.
  - Logging in - We used the developer tools on FireFox to find the source HTML to locate the names of fields and where the csrfTokens would be located.
  - Throughout all of this we will scan every page for urls following the rules we described above and we will also be looking for keys that follow the style of class='secret_flag'
- Beginning to throw down some code:
  - Absolute first step was formatting the command line interactions.
  - We then started with the login method which proved to be far more difficult than expected. We figured out quickly that a GET request was required to get the csrf token, then we found there was two required. One was located in the header of the GET request and another was within the body. 
  - After two days of stuggling with the POST request, we got the cookie we were hungry for and a session id to begin collecting urls to inspect for flags.
- Page Parsing:
  - We first generalized the way we were sending GET requests.
  - Next, we implemented methods to extract urls from the html pages and organized them in a dict to store ones we've seen and an array to store ones we will be visiting next. We initially were thinking about using html.parse, but honestly it was more work than it was to write our own versions of those methods. We wrote a dynamic way of finiding pertinent urls and grabbed them in total regardless of the total size of the listed url.
  - Next we began to search for flags in all the pages we visited. 
- Chunking was the next thing to solve, if a page was cut off we could tell by the size field within the header, and we would then combine part of the next packet with the previous one.
- We found various edge cases where the requests or responses would get damaged, and with these we added the urls they came from back into the queue to be traversed again.

## Challenges:
- First main challenge we face was getting our POST request formatted. This was partly a product of not formatting our initial GET request to get the two csrf tokens correctly. In addition to this, we needed to look at the developer tools in FireFox to get a good example of what a POST request for that site should look like.
- A challenge was how we were going to store and update urls to view. For instance, when running a while loop on an array, how we would update as we found new urls.
- Another challenge was the fact that the web server spontaneously starts to send nothing back and closes the connection. This was a major initial hurdle to get over. We handled it by opening a new connection if we get a 400 error or no data in our response.
- Chunking was also very difficult to solve mainly because it was hard to create a consistent way of testing.
- We weren't getting all of the flags every time so we created a counter to see how many pages we visited and saw a huge variance. This allowed us to zero in on some edge cases that would cut off links such as the aforementioned chunking.

## Testing:
- The initial primary way of testing was a lot of incremental print statements. Every time we thought something would work, we printed the outcome, specifically responses.
- We also tested the command line specifications to ensure we handled invalid inputs correctly.
- Another way we "stress tested" this program was by writing a small bash script 'runner.sh' that would execute the code 30 times and let it run overnight. This was to check for consistency.

