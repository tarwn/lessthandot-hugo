---
title: Automated Keyword Extraction – TF-IDF, RAKE, and TextRank
author: Eli Weinstock-Herman (tarwn)
type: post
date: 2016-11-21T21:09:50+00:00
url: /index.php/artificial-intelligence/automated-keyword-extraction-tf-idf-rake-and-textrank/
featured_image: /wp-content/uploads/2016/11/pythoncode.png
views:
  - 12143
rp4wp_auto_linked:
  - 1
categories:
  - Artificial Intelligence
tags:
  - python
  - RAKE
  - text summarization
  - TextRank
  - TF-IDF

---
After initially playing around with text processing in [my prior post][1], I added an additional algorithm and cleaned up the logic to make it easier to perform test runs and reuse later. I tweaked the RAKE algorithm implementation and added TextRank into the mix, with full sample code and links to sources available. I&#8217;m also using a read-through cache of the unprocessed and processed files so I can see the content and tweak the cleanse logic. 

<div style="margin: 1em 0; padding: 1em; background-color: #FFFFCC">
  Context: The ultimate goal is to build a script that could process through 6 years of my bookmarked reading and extract out keywords, so I could do some trend analysis on how my reading has changed over time and maybe later build a supervised model with that data to analyze new online posts and produce a &#8220;worth my time or not&#8221; score.
</div>

The first step was to increase my hands-on knowledge of text processing and identify potential algorithms. I used Python as the programming language, 5 sample posts from my own website, and two algorithms: TF-IDF and RAKE. I learned how these algorithms work, that data cleansing is an incredibly important step, and that python package management has trailed significantly behind most of the other languages I use day-to-day.

The code for this post is available here: <https://github.com/tarwn/bookmark_analysis/tree/master/exploration/v2>

Let me start with the results, then I&#8217;ll jump into the algorithms and code.

## Keyword Extract Results

These results are a subset of a larger set using TF-IDF, RAKE, and TextRank for the latest 50 posts I&#8217;ve written on LessThandot. I chose a larger sample because TF-IDF relies on frequency of words found in other documents as part of the score, so I wanted a large enough pool to help it shine (or not). 

Here are results for two recent posts:

URL: <http://www.tiernok.com/posts/continuous-javascript-test-execution-with-wallabyjs.html>

<pre>RAKE:
 * radiates test statuses directly
 * test markers turn green/red
 * continuous javascript test execution
 * test marker turns red
 * open visual studio code
TF-IDF:
 * wallaby
 * karma
 * runners
 * visual
 * studio
TextRank:
 * requirejs configuration
 * wallaby-vscode wallaby
 * install wallaby-vscode
 * continuous javascript
 * development feedback</pre>

URL: <http://www.tiernok.com/posts/stop-manually-updating-your-jasmine-specrunner.html>

<pre>RAKE:
 * greatly simplify future updates
 * self-hosted web â updating assets
 * stop manually updating
 * normal debugging sessions
 * tiny sample project
TF-IDF:
 * allspecs
 * specrunner
 * spec
 * gulp
 * file
TextRank:
 * self-hosted website
 * vanilla bootloader
 * jasmine specrunner
 * custom bootloader
 * similar approach</pre>

RAKE optimizes towards long keywords, which provides accurate phrases that aren&#8217;t terribly useful as keywords. TF-IDF has pulled out some viable keywords. TextRank seems to most frequently pull out the best candidate set of the 3 by landing somewhere between the twp.

## Algorithms

If you read the prior post, you can skip past the first two to TextRank.

### TF-IDF

TF-IDF stands for Text Frequency Inverse Document Frequency. At a high level, a TF-IDF score finds the words that have the highest ratio of occurring in the current document vs the frequency of occurring in the larger set of documents.

The code for the TF-IDF implementation was based on [this post by Steven Loria][2].

### RAKE Algorithm

The RAKE algorithm is described in the book Text Mining Applications and Theory by Michael W Berry ([amazon][3], [wiley.com][4]):

1. Candidates are extracted from the text by finding strings of words that do not include phrase delimiters or stop words (a, the, of, etc). This produces the list of candidate keywords/phrases.

2. A Co-occurrence graph is built to identify the frequency that words are associated together in those phrases. Here is a good outline of how co-occurence graphs are built: [Mining Twitter Data with Python (Part 4: Rugby and Term Co-occurrences)][5]

3. A score is calculated for each phrase that is the sum of the individual word&#8217;s scores from the co-occurrence graph. An individual word score is calculated as the degree (number of times it appears + number of additional words it appears with) of a word divided by it&#8217;s frequency (number of times it appears), which weights towards longer phrases.

4. Adjoining keywords are included if they occur more than twice in the document and score high enough. An adjoining keyword is two keyword phrases with a stop word between them.

5. The top T keywords are then extracted from the content, where T is 1/3rd of the number of words in the graph

The implementation I used is based on [python-rake][6], with some modifications for providing custom thresholds based on [this post][7].

### TextRank Algorithm

TextRank is described in the paper [TextRank: Bringing Order into Texts][8] by Rada Mihalcea and Paul Tarau. 

In general, TextRank creates a graph of the words and relationships between them from a document, then identifies the most important vertices of the graph (words) based on importance scores calculated recursively from the entire graph.

1. Candidates are extracted from the text via sentence and then word parsing to produce a list of words to be evaluated. The words are annotated with part of speech tags (noun, verb, etc) to better differentiate syntactic use

2. Each word is then added to the graph and relationships are added between the word and others in a sliding window around the word

3. A ranking algorithm is run on each vertex for several iterations, updating all of the word scores based on the related word scores, until the scores stabilize &#8211; the research paper notes this is typically 20-30 iterations

4. The words are sorted and the top N are kept (N is typically 1/3rd of the words)

5. A post-processing step loops back through the initial candidate list and identifies words that appear next to one another and merges the two entries from the scored results into a single multi-word entry

I used [this TextRank implementation][9] as a base, modifying it to return the numeric score with the keywords so I could isolate the top 5 like I have for other algorithms. My solution for multi-word scores was simple addition, for no reason better than it was good enough for what I&#8217;m doing.

## Making it all work

As I mentioned earlier, the initial cleansing of the data was a critical step. I wrote a small data loader that accepts a cache folder and a cleanse method and surfaces a call to load the text for a given URL. 

[ContentLoader/contentloader.py][10]

When called, ContentLoader identifies whether HTML or processed Text content is already available, and if not, downloads and/or processes the HTML, caching the results if work was required, and returning just the text. This allows lots of fast repetition on the algorithm side when I am tweaking those, lets me see the raw html and text results of the cleanse function, and allows me to easily rerun part of thw cleanse by removing the text or HTML files.

The barebones of my logic to download content and run it through the algorithms above is:

<pre>def execute(cleanse_method, pages):
    """Execute RAKE and TF-IDF algorithms on each page and output top scoring phrases"""

    #1: Initialize a URL reader with local caching to be kind to the internet
    reader = contentloader.CacheableReader(CACHE_FOLDER, cleanse_method)

    #2: Collect raw text for pages
    processed_pages = []
    for page in pages:
        page_text = reader.get_site_text(page)
        processed_pages.append({"url": page, "text": page_text})

    #3: RAKE keywords for each page
    rake = RAKE.Rake(RAKE_STOPLIST, min_char_length=2, max_words_length=5)
    for page in processed_pages:
        page["rake_results"] = rake.run(page["text"])

    #4: TF-IDF keywords for processed text
    document_frequencies = {}
    document_count = len(processed_pages)
    for page in processed_pages:
        page["tfidf_frequencies"] = tfidf.get_word_frequencies(page["text"])
        for word in page["tfidf_frequencies"]:
            document_frequencies.setdefault(word, 0)
            document_frequencies[word] += 1

    sortby = lambda x: x[1]["score"]
    for page in processed_pages:
        for word in page["tfidf_frequencies"].items():
            word_frequency = word[1]["frequency"]
            docs_with_word = document_frequencies[word[0]]
            word[1]["score"] = tfidf.calculate(word_frequency, document_count, docs_with_word)

        page["tfidf_results"] = sorted(page["tfidf_frequencies"].items(), key=sortby, reverse=True)

    #5. TextRank
    for page in processed_pages:
        textrank_results = textrank.extractKeyphrases(page["text"])
        page["textrank_results"] = sorted(textrank_results.items(), key=lambda x: x[1], reverse=True)

    #6. Results
    for page in processed_pages:
        # print results output

def cleanse_tiernok_html(html):
    """Cleanse function for tiernok.com blog posts"""
   # ... BeautifulSoup Logic here ...

# get links because I'm too lazy to copy/pasta
def get_test_links():
    # ... Logic to scrap post archive links here ...

# run algorithms with the cleanse method above for the gathered list of links
test_links = get_test_links()[:50]
execute(cleanse_tiernok_html, test_links)</pre>

Compared to my exploratory scripts in the prior version, now I have something easy to read and usable as the base for other projects. The cleanse method is provided as a function so the underlying contentloader isn&#8217;t tied directly to my site&#8217;s page layout and the links are gathered independently for the same reason.

## Results

TextRank provided the best results for what I&#8217;m trying to do, but seemed the slowest by a lot. 

**Time:** At 50 documents the RAKE and TF-IDF implementations took about 2 seconds each, while TextRank took over 6.5 <u>minutes</u>. This is a comparison of three implementations I downloaded from the internet, though, so I&#8217;m going to dig deeper or write my own implementation of TextRank to see if I can locate the bottleneck.

**Best fit for my goal:** For the purposes of identifying longer term trends (common subjects across all of my documents, &#8220;C#&#8221; or &#8220;JavaScript&#8221;), TextRank or RAKE is going to generally be the best choice, as TF-IDF will likely score common words across many documents lower (Inverse Document Frequency).

**Other Alternative:** I also evaluated the Text Analytics option for [Azure Cognitive Services][11]. It was quick to get running and has a free tier that would be plenty for me (1000 calls/month). Unfortunately the [Key Phrases API][12] surfaces only the keywords (1/3rd of the phrases, at a guess) and not the associated scores, so it wouldn&#8217;t work for this use (which is unfortunate, parallel cloud execution would be nice to have for this project).

 [1]: /index.php/artificial-intelligence/to-build-automatic-bookmarking-unsupervised-text-classification/ "To Build Automatic Bookmarking – Unsupervised Text Classification"
 [2]: http://stevenloria.com/finding-important-words-in-a-document-using-tf-idf/
 [3]: https://www.amazon.com/Text-Mining-Applications-Michael-Berry/dp/0470749822/
 [4]: http://onlinelibrary.wiley.com/book/10.1002/9780470689646
 [5]: https://marcobonzanini.com/2015/03/23/mining-twitter-data-with-python-part-4-rugby-and-term-co-occurrences/
 [6]: https://pypi.python.org/pypi/python-rake/1.0.5
 [7]: https://www.airpair.com/nlp/keyword-extraction-tutorial
 [8]: http://web.eecs.umich.edu/~mihalcea/papers/mihalcea.emnlp04.pdf
 [9]: https://github.com/davidadamojr/TextRank
 [10]: https://github.com/tarwn/bookmark_analysis/tree/master/exploration/v2/contentloader
 [11]: https://azure.microsoft.com/en-us/services/cognitive-services/
 [12]: https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6 "Azure Machine Learning - Text Analytics API - Key Phrases"