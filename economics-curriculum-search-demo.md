# Using the Open Library Search API

The Open Library Search API is a very powerful and under-utilized tool. This demo shows how you can use the Search API to build a subject-specific curriculum for any grade level - say, "civil rights curriculum for a high school student."  

To do this, we show how to use three signals from the index:
1. **Filter by subject** — What topic should the book be about?
2. **Filter by Open Syllabus Project signal** — How many times does a book appear in college syllabi?
3. **Filter by Lexile band** — What reading level is the book?

![Open Library search results for a civil rights query using subject, OSP count, and Lexile in the search bar](./openlibrary-search-civil-rights-screenshot.png)

You can test your queries by pasting the query string directly in the [search box at OpenLibrary](https://openlibrary.org/search?) or by constructing the URL using parameters as described in https://openlibrary.org/search/howto and pasting in the browser.

## Subject

The subject filter allows you to filter books by subject/topic - what is this book about?

However, the subject filter is a loose match. There is no official, complete list of allowed values you can download and pick from. Instead, works carry **subject tags** that grow over time: librarians and volunteers add them without enforcing one global standard on every book.

Subject tags also do not have any relational/parent-child structure. For exaxmple, books with `subject:"civil rights"` are not a subset of books with `subject:"history"`.

For example, the query `subject:"civil rights"` includes historical fiction in its results, such as *Homeland* by Cory Doctorow.

The query `subject:"civil rights" AND subject:"nonfiction"` eliminates *Homeland*, but also incorrectly eliminates *I Have a Dream* by Martin Luther King Jr.

In this case, `subject:"civil rights" AND subject:"history"` gave the best results of civil rights nonfiction books. 

In general, subject filtering is more of an art than a science - users are encouraged to try a variety of keywords.


## Open Syllabus Project

The [Open Syllabus Project](https://opensyllabus.org/) (OSP) collects large sets of course syllabi from higher education institutions and aggregates which books and texts are assigned. Because the OSP count is an aggregate, the API has several limitations:
- You cannot find a list of which high schools or colleges assigned a given book.
- You cannot find which courses a book was assigned for.
- The OSP count lacks complete coverage of all syllabi worldwide — only what OSP has in **their** collection.

The most basic usage is to filter by OSP count: `subject:"civil rights" AND osp_count:[100 TO *]` shows results with OSP count >= 100. By default, results are sorted by relevance.

Here are some sample results from running this query in April 2026. Note that you may not get the exact same results, as relevance order can shift as records change. You can see live results [here](https://openlibrary.org/search?q=subject%3A%22civil+rights%22+AND+osp_count%3A%5B100+TO+*%5D).
| Title | Authors | Lexile | OSP count |
|-------|---------|--------|-------------|
| [*Up from Slavery*](https://openlibrary.org/works/OL357501W) | Booker T. Washington | — | 924 |
| [*Rights of Man*](https://openlibrary.org/works/OL60359W) | Thomas Paine | — | 1458 |
| [*The Souls of Black Folk*](https://openlibrary.org/works/OL28577W) | W. E. B. Du Bois | — | 4328 |
| [*Twelve years a slave*](https://openlibrary.org/works/OL78871W) | Solomon Northup | — | 432 |
| [*The fire next time*](https://openlibrary.org/works/OL2683364W) | James Baldwin | — | 1223 |

You might want to sort by OSP count rather than filtering if you're not sure what a good filtering threshold would be. 

Unfortunately, the **search box** and UI on [openlibrary.org](https://openlibrary.org) do not support sorting by OSP count.

Instead, after you search, add **`&sort=osp_count+desc`** (or `+asc`) to the address bar, for example: https://openlibrary.org/search?q=civil+rights&sort=osp_count+desc.

**Sample results** when using that sort (April 2026 snapshot; order and counts can change):
| Title | Authors | Lexile | OSP count |
|-------|---------|--------|-------------|
| [*The Souls of Black Folk*](https://openlibrary.org/works/OL28577W) | W. E. B. Du Bois | — | 4328 |
| [*The New Jim Crow*](https://openlibrary.org/works/OL13826369W) | Michelle Alexander | — | 3920 |
| [*Letter from the Birmingham jail*](https://openlibrary.org/works/OL1945913W) | Martin Luther King Jr., Dion Graham | — | 3184 |

The OpenLibrary UI does not show Lexile or OSP count. To see those values, use **`GET https://openlibrary.org/search.json` with the same query parameters as the normal results page (`/search?…`), but use the path `/search.json` instead. Paste the full URL into a browser address bar or request it with **`curl`** to see the raw JSON.

In the previous example of https://openlibrary.org/search?q=civil+rights&sort=osp_count+desc, the corresponding JSON output can be seen at: [https://openlibrary.org/search.json?q=civil+rights&sort=osp_count+desc](https://openlibrary.org/search.json?q=civil+rights&sort=osp_count+desc).

### Lexile - Reading Level

[Lexile](https://www.lexile.com/) measures the “reading difficulty” of a text. Specifically, Lexile captures **linguistic complexity** (vocabulary, syntax, cohesion).

Here's a rough guide mapping lexile scores to reading level:

| Stage | Typical Lexile range (text)* | Example texts (approx. Lexile)** |
|-------|------------------------------|-----------------------------------|
| Grade 1 | 190L–530L | [*Green Eggs and Ham*](https://openlibrary.org/works/OL1898308W) (210L) |
| Grade 2 | 420L–650L | [*Henry and Mudge*](https://openlibrary.org/works/OL64034W) (450L) |
| Grade 3 | 520L–820L | [*Charlotte’s Web*](https://openlibrary.org/works/OL483391W) (680L) |
| Grade 4 | 740L–940L | [*Harry Potter and the Sorcerer’s Stone*](https://openlibrary.org/works/OL28649132W) (880L) |
| Grades 5–6 | 830L–1070L | [*Island of the Blue Dolphins*](https://openlibrary.org/works/OL4466828W) (1000L); [*The Hobbit*](https://openlibrary.org/works/OL27482W) (1000L) |
| Grades 7–8 | 970L–1160L | [*Fahrenheit 451*](https://openlibrary.org/works/OL103123W) (1030L); [*The Great Gatsby*](https://openlibrary.org/works/OL468431W) (1010L) |
| Grades 9–10 | 1050L–1230L | [*Brave New World*](https://openlibrary.org/works/OL64365W) (1090L); [*1984*](https://openlibrary.org/works/OL1168083W) (1170L) |
| Grades 11–12 | 1185L–1385L | [*The Scarlet Letter*](https://openlibrary.org/works/OL455305W) (1200L); [*Moby-Dick*](https://openlibrary.org/works/OL102749W) (1200L–1380L) |
| College | 1185L–1390L | [*A Brief History of Time*](https://openlibrary.org/works/OL1892617W) (1200L); [*The Republic*](https://openlibrary.org/works/OL34723025W) (1310L) |
| Beyond | 1400L+ | [*Guns, germs, and steel*](https://openlibrary.org/works/OL276558W) (1440L); [*Absalom, Absalom!*](https://openlibrary.org/works/OL82928W) (1570L) |
| No Lexile | — | [*Understanding Supreme Court opinions*](https://openlibrary.org/works/OL3244259W); [*Infinite Jest*](https://openlibrary.org/works/OL2943602W); [*Advanced Engineering Mathematics*](https://openlibrary.org/works/OL1400344W) |

Unfortunately, 99% of books on Open Library do not have a Lexile score. MetaMetrics, the creator of Lexile, does not score every published book. Whether a book is scored depends on publisher and partner programs. 

For example, to create a civil rights curricula for elementary school children, query `subject:"civil rights" AND lexile:[100 TO 900] AND osp_count:[1 TO *]`. For high school students, query `subject:"civil rights" AND lexile:[1000 TO 1385]`.

---

## Debugging Queries

### No results were returned.

Lexile and OSP are not on every work, and they don’t always overlap.

- A query that requires `subject_key:economics` plus a Lexile band plus `osp_count:[1 TO *]` may legitimately return `numFound: 0`.
- Fewer constraints (for example, drop `osp_count:[1 TO *]` but keep `sort=osp_count desc`) still surfaces `osp_count` when present and ranks OSP-heavy titles first, while showing other matches with missing or lower OSP.

### Results included off-topic books.

Search is fuzzy and imprecise by default. A book can surface because a substring or related heading matches, not only because it is “really” about your topic in everyday terms.

Curriculum builders should expect to combine headings (`subject:"civil rights" AND subject:history`), try synonyms, or start from known good books and reuse their exact `subject_key` values.

---
## References
- [Open Library Search Tips](https://openlibrary.org/search/howto) — `q` syntax, `subject_key`, ranges.
- [Search API](https://openlibrary.org/dev/docs/api/search) — `fields`, `sort`, pagination, `limit`.
