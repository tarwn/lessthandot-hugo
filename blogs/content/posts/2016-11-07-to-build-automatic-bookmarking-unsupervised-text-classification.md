---
title: To Build Automatic Bookmarking – Unsupervised Text Classification
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-11-07T13:39:02+00:00
ID: 4769
url: /index.php/artificial-intelligence/to-build-automatic-bookmarking-unsupervised-text-classification/
views:
  - 4040
rp4wp_auto_linked:
  - 1
categories:
  - Artificial Intelligence
tags:
  - python
  - RAKE
  - text summarization
  - TF-IDF

---
I've been bookmarking all of my online reading for the past 7 years and recently started thinking about using that dataset to dig into trends in my past reading and potentially build a model to start scoring content I haven't read yet. Even though I have manual keywords for each entry, I decided to look into what I could get with unsupervised text classification techniques to balance out the fact that I had entered those labels over long periods of time.

My first goal is to programmatically extract keywords from consistently formatted blog posts on static website pages.

<div style="margin: 1em 0; padding: 1em; background-color: #FFFFCC">
  <b>Warning:</b> This isn't an expert post on text classification or natural language processing. It's a mildly rambling journey of a developer making general progress and the code, libraries, and resources used along the way.</p> 
  
  <p>
    For context, I am not an expert. Machine learning has been an interest since college, so I know some of the general terms at a high level and have written a number of smaller supervised systems, but haven't actually played with text classification or natural language processing before.
  </p>
</div>

I learn best by doing, so on top of some of the high level knowledge I added a bunch of reading and hacking around in the evenings (I learn best by doing things). Here is the subset that helped the most:

  * [Intro to Automatic Keyphrase Extraction][1]
  * [Tutorial: Finding Important Words in Text Using TF-IDF][2]
  * [NLP keyword extraction tutorial with RAKE and Maui][3]

I also leaned on the [The Hitchhiker's Guide to Python: HTML Scraping][4], because I only dip into python about every 5 years and was pretty rusty.

## Using TF-IDF

TF-IDF stands for Text Frequency Inverse Document Frequency. At a high level, a TF-IDF score finds the words that have the highest ratio of occurring in the current document vs frequency of occurring in larger set of documents.

I used a series of 5 of my own blog posts to test with:

```py
pages = [
    'http://tiernok.com/posts/continuous-javascript-test-execution-with-wallabyjs.html',
    'http://tiernok.com/posts/stop-manually-updating-your-jasmine-specrunner.html',
    'http://tiernok.com/posts/self-hosted-web-updating-assets-without-restarting-the-debugger.html',
    'http://tiernok.com/posts/asp-net-single-sign-on-against-office365-with-oauth2.html',
    'http://tiernok.com/posts/improved-teamcity-net-build-warnings.html'
]
```
Starting with a set of my own posts meant the consistent would be in a consistent format, I would have some ideas on what I expected the keywords to be, and I could defer things like local content caching logic without running up someone else's bill or messing up their page statistics.

_Please be kind to my site if you run these scripts yourself, btw_

I played around with several ways to extract the text, but ended up on the [requests][5] module for scraping, [html2text][6] for extracting text, and [TextBlob][7] for the analysis, basing the script on [Steven Loria's post][8] and using his functions for the scoring.

After a few iterations where I tried to resolve pluralization and grouping things by common word roots (lemmetizing), I ended up back at a simple implementation that doesn't take forever to run:

**[tfidf.py][9]**

```py
# 1: Get content of site using requests and html2test
def get_site_text(url):
    resp = requests.get(url)
    resp.raise_for_status()
    html = resp.text
    return html2text.html2text(html)

# 2: Score each word for an individual page against the full set of pages
def score_page(blob, blobs):
    scores = {word: tdidf(word, blob, blobs) for word in blob.words if len(word) > 2}
    return sorted(scores.items(), key=lambda x: x[1], reverse=True)

# 3: For each URL in the page list, get the text content and a TextBlob for the content
completed_pages = []
for page in pages:
    print('Processing %s' % page)
    try:
        raw = get_site_text(page).lower()
        completed_pages.append({"page": page, "raw": raw, "blob": tb(raw)})
    except Exception as exc:
        print('Couldn\'t download %s, error: %s' % (page, exc))
        continue

# 4: For each page, calculate TD-IDF scores and output top 5 scoring words
for page in completed_pages:
    print('Scoring %s' % page["page"])
    page_scores = score_page(page["blob"], [page["blob"] for page in completed_pages])
    for word, score in page_scores[:5]:
        print("\rWord: {}, TF-IDF: {}".format(word, round(score, 5)))
```
I love python for it's readability, but documentation and figuring out which library one should be using for basic things like executing web requests ate up a bunch of my time (this is the 3rd web library, 4th HTML to text method, and 2nd library for analyzing content).

This is the top words it gave me for the first entry, a post about a real-time JavaScript test runner using Microsoft's VS Code IDE for samples:

```text
Scoring http://tiernok.com/posts/continuous-javascript-test-execution-with-wallabyjs.html
Word: wallaby, TF-IDF: 0.01319
Word: baseurl, TF-IDF: 0.01116
Word: tests, TF-IDF: 0.00962
Word: false, TF-IDF: 0.00812
Word: isusingwallaby, TF-IDF: 0.00609
```
Apparently I didn't use explicit “false” values frequently enough in the prior posts (or at least not as frequently as I used the word Javascript). 

This bring us to the reasons it won't fit for what I'm doing:

1. If I compare all of my content against my content, a lot of the real keywords are going to end up with high IDF values simply because I am going to use them in a lot of posts (or read about them a lot when I apply it to bookmarking other people's content)

2. It's slow. Granted, I'm doing this the worst possible way right now, by analyzing the whole set of words in the whole set of documents for each page, so I could optimize a lot simply by building an overall hashtable of words and document frequencies and per-document hashtables of words and frequencies, but this is only going to optimize well because I use the same words a lot, which brings us back to #1.

So, still a useful tool in the bag and could be interesting comparing a small number of documents to more general written works to not lose the industry specific terms, but not really the answer to what I'm looking for.

## RAKE: Rapid Automatic Keyword Extraction

RAKE is an [unsupervised][10] algorithm designed to quickly extract keywords from longer form text very efficiently and without comparison to a larger body of works.

### How RAKE works

The RAKE algorithm is described in the book Text Mining Applications and Theory by Michael W Berry ([amazon][11], [wiley.com][12]):

1. Candidates are extracted from the text by finding strings of words that do not include phrase delimiters or stop words (a, the, of, etc). This produces the list of candidate keywords/phrases.

2. A Co-occurrence graph is built to identify the frequency that words are associated together in those phrases. Here is a good outline of how co-occurence graphs are built: [Mining Twitter Data with Python (Part 4: Rugby and Term Co-occurrences)][13]

3. A score is calculated for each phrase that is the sum of the individual word's scores from the co-occurrence graph. An individual word score is calculated as the degree (number of times it appears + number of additional words it appears with) of a word divided by it's frequency (number of times it appears), which weights towards longer phrases.

4. Adjoining keywords are included if they occur more than twice in the document and score high enough. An adjoining keyword is two keyword phrases with a stop word between them.

5. The top T keywords are then extracted from the content, where T is 1/3rd of the number of words in the graph

Which sounds like it should resolve both the speed and watering down effects from TF-IDF.

### Implementation using RAKE

Luckily, there are a few implementations for RAKE already for python. I'll be using the highest google result, [python-rake][14].

**[rake.py][15]**

```py
# 1: Method to get content of site using requests and html2test
def get_site_text(url):
    resp = requests.get(url)
    resp.raise_for_status()
    html = resp.text
    return html2text.html2text(html)

#2: Import stopwords from an external file
Rake = RAKE.Rake('stoplists/SmartStoplist.txt')

#3: Process each file and display top keywords
for page in pages:
    print('Processing %s' % page)
    page_text = get_site_text(page)
    keywords = Rake.run(page_text)
    for keyword, score in keywords[:5]:
        print('Keyword: %s, score: %d' % (keyword, score))

end_time = time.time() - start_time
print('Done. Elapsed: %d' % end_time)
```
And for the first entry in the list of pages, this nets us:

<pre type="text">Processing http://tiernok.com/posts/continuous-javascript-test-execution-with-wallabyjs.html
Keyword: radiates test statuses directly, score: 14
Keyword: test markers
turn green/red, score: 13
Keyword: # [continuous javascript test execution, score: 12
Keyword: open visual studio code, score: 12
Keyword: ext install\nwallaby-vscode\nwallaby
</pre>

Well…that didn't go as planned.

Rake seemed to get closer to a good set of results and was twice as fast as the TF-IDF version (which, as written, will get progressively worse with every link we add to the pile). Unfortunately, we're running into a Garbage-In Garbage-Out problem.

## Better algorithms don't give us cleaner input data

One of the biggest issues with AI/ML work is making sure you have a clean data set. In this case, there is no way for these two algorithms to know to skip things like code entries and content from the header and footer of the site.

Because I'm working with really consistent input, I can add some cleansing logic to extract just the blog posts from each page and throw away sections of code. 

### Cleaner Content using BeautifulSoup

Using the [BeautifulSoup][16] module, I can grab just the article from each page and extract (remove) the code blocks from each entry before processing.

**[betterData.py][17]**

```py
def get_site_text(url):
    resp = requests.get(url)
    resp.raise_for_status()
    html = resp.text
    soup = BeautifulSoup(html, "html.parser")
    content = soup.find('article')

    # remove code blocks
    for element in content.findAll(class_="bwp-syntax-block"):
        element.extract()

    # remove comments filler at bottom and subtext under post title at top
    for element in content.findAll(class_="ep-post-comments"):
        element.extract()
    for element in content.findAll(class_="ep-post-subtext"):
        element.extract()

    # lower case for better matching
    return content.get_text().lower()
```
After adding this little bit of cleanup and running it back through the same logic as above, the results are a lot better:

```text
Page http://tiernok.com/posts/continuous-javascript-test-execution-with-wallabyjs.html
    TF-IDF Keywords:
        Word: wallaby, TF-IDF: 0.0133
        Word: test, TF-IDF: 0.00742
        Word: ncrunch, TF-IDF: 0.00665
        Word: tests, TF-IDF: 0.00649
        Word: karma, TF-IDF: 0.00556
    RAKE Keywords:
        Keyword: radiates test statuses directly, score: 15
        Keyword: test markers turn green/red, score: 14
        Keyword: continuous javascript test execution, score: 13
        Keyword: test marker turns red, score: 13
        Keyword: open visual studio code, score: 12
```
Now with a cleaner content to work with, TF-IDF has pulled out a pretty good set of keywords. RAKE has pulled out a good set of phrases from the document, in terms of importance, but not really something I would use for keywords. Neither is knocking it out of the ballpark yet, so more work is needed.

## And thus it goes…

This started as some scratch code to try and generate keywords for a consistent set of HTML pages, with the intent of applying what I built here to a diverse set (everything I've read and remembered to bookmark in the past 7 years). My next steps are going to be to try TextRank against the data to evaluate how it does and to modify the RAKE code to accept some constraints to see if, for instance, a limit of 2-3 word phrases nets a better set.

Along the way, I also realized that LessThanDot has an awesome set of content available that would be interesting to analyze (over 1800 posts) and between tags, titles, and categories there is some labeling in place that might enable some supervised learning techniques.

 [1]: http://bdewilde.github.io/blog/2014/09/23/intro-to-automatic-keyphrase-extraction/
 [2]: http://stevenloria.com/finding-important-words-in-a-document-using-tf-idf/
 [3]: https://www.airpair.com/nlp/keyword-extraction-tutorial
 [4]: http://docs.python-guide.org/en/latest/scenarios/scrape/
 [5]: http://docs.python-requests.org/en/master/
 [6]: https://github.com/aaronsw/html2text
 [7]: https://textblob.readthedocs.io/en/dev/
 [8]: http://stevenloria.com/finding-important-words-in-a-document-using-tf-idf/ "Tutorial: Finding Important Words in Text Using TF-IDF"
 [9]: https://github.com/tarwn/bookmark_analysis/blob/master/exploration/tfidf.py "tfidf.py in github/tarwn/bookmark_analysis"
 [10]: https://en.wikipedia.org/wiki/Unsupervised_learning
 [11]: https://www.amazon.com/Text-Mining-Applications-Michael-Berry/dp/0470749822/
 [12]: http://onlinelibrary.wiley.com/book/10.1002/9780470689646
 [13]: https://marcobonzanini.com/2015/03/23/mining-twitter-data-with-python-part-4-rugby-and-term-co-occurrences/
 [14]: https://pypi.python.org/pypi/python-rake/1.0.5
 [15]: https://github.com/tarwn/bookmark_analysis/blob/master/exploration/rake.py "rake.py in github/tarwn/bookmark_analysis"
 [16]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/#
 [17]: https://github.com/tarwn/bookmark_analysis/blob/master/exploration/betterData.py "betterData.py in github/tarwn/bookmark_analysis"