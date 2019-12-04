---
title: How to do Trigram searches in postgreSQL with rails
date: 2015-01-14 03:29:34 Z
layout: post
---

Postgresql as we know is one of the most matured and robust database out there. It is open source, provides more features than any of its counterparts. It's also the [second](http://ocelot.ca/blog/blog/2013/10/14/the-third-most-popular-open-source-dbms/) most used open source DBMS out there. If you aren't using it [download](http://www.postgresql.org/download/) and get started.

Today we shall look at how to perform trigram indexed searches and trigram combined with postgres full text search. Let's get started.

#### What is trigram?
An n-gram of three letter sequence is known as trigram. What is ngram then? From wikipedia,

>In the fields of computational linguistics and probability, an n-gram is a contiguous sequence of n items
from a given sequence of text or speech.

So what does it mean exactly? It means finding matching words by maintaining variable sequences of characters of a word.

What trigram does is, it splits the given word into three letter sequence at the max and does a similarity search against them in order to find the relevant words to the given word. This comes very useful in terms of autocorrection.

For example, if the available word is *google*, trigram splits it like
>`g`, `go`, `goo`, `oog`, `ogl`, `gle`, `le`

And assume the user searches for *googlr* a typo for *google*, for which the trigrams are calculated
> `g`, `go`, `goo`, `oog`, `ogl`, `glr`, `lr`

And the similiarity is calculated using the number of trigrams they share, which in our case is quite good. So this comes before. if there is say, another word *noogle* in the DB which has low similiarity with *googlr*, would be avoided or shown in the last if the filter value is set lower.

#### Trigram in PostgreSQL
Postgres supports trigram through an extension called [pg_trgm](http://www.postgresql.org/docs/current/static/pgtrgm.html) which is a Postgres supported extension. The feature this extension provides out of the box are,

* similarity(text1, text2) - Calculates the similarity index between text1 and text2 in the scale of 0 to 1, with 0 being the least similiar.

* show_trgm(text)- Lists the trigrams that could be calculated from the given text, like we did above.

* show_limit() - The set filter used by % (look below). Similarity index above this limit is only shown while performing trigram search. The default limit is 0.3.

* set_limit(real) - Sets the limit to be used by %.

* text1 % text2 - Returns true if the similarity between text1 and text2 is above the above set limit.

* text1 <-> text2 - Distance operator, inverse of similarity. Returns the distance between text1 and text2.

* gist\_trgm\_ops and gin\_trgm\_ops- Allows to build GiST or GIN index respectively over a text column for faster similarity search.

Let's get started.

#### Sample app
For the sample app we need a larger DB, so I created one with some 1.5mn rows generated using [Faker](https://github.com/stympy/faker) gem. The rows contains varying sentences generated from Latin words, but at the most some 217 words shuffled.

#### Install pg_trgm extension
If you haven't yet, install the `pg_trgm` module first. I suggest using a migration to do this instead of doing it directly in psql console. Create a migration and add the below line to it,

`execute "create extension pg_trgm;"`

Now execute the migration. This will install the extension pg_trgm to the postgres in the system.

#### Add index
Next up, add the relevant index to the column that we are going to search. At this point I really can't recommend GiST or GIN since I've had varying performance [differences](https://twitter.com/avinoth_/status/555044180422979584) in the past. So, create a migration and add the index you are comfortable with. Below is the code I used,

`execute "CREATE INDEX name_similarity_idx ON posts USING gist (name gist_trgm_ops);"`

Execute the migration and you are good with index now.

#### Now to the search query
We are all set now to do the trigram searches. Add a method in your model to put the search queries and put the below code into it. for example,

<script src="https://gist.github.com/avinoth/b77f6bc23f9372b32429.js"></script>

and we can call this method from the controller like,


	@posts = Post.text_search(params[:query])


With all these set, go to the rails app and search with typos and see the magic of similar words showing up.

We can combine this search by generating a separate table containing all the unique words from this column and perform the search there. That would increase the speed of search by a good chunk, but it's quite useless in the case of fuzzy text search or non-english words search.

There is a gem called [pg_search](https://github.com/Casecommons/pg_search) that provides all of the above out of the box, but for some reason the trigram search from this gem is slow for me than the raw SQL.

Anyhow, play around a bit around GIN and GiST indexes, trigram and try working it along with `ts_vectors` column and see how you could combine them. See you.
