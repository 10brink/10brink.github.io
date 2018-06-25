---
layout: post
title: Using Natural Language Processing to Topic Model Supreme Court Cases
published: true
---


For this project I used Natural Language Processing and unsupervised learning to preform topic modeling on supreme court
cases/opinions.

The tools I used were NLTK, sklearn and spacey, as well as beautiful soup for webscraping. I scraped the site
https://caselaw.findlaw.com/court/us-supreme-court/ to get all the opinions over the history of the US supreme court (23,000 case documents!). 
My original intent was to find which supreme court justices were most similar to each other, 
but my model mainly just picked out the various topics that they wrote about 
(for example fourth amendment cases, sixth amendment cases, labor laws, etc.) so I pivoted my focus to do topic modeling 
on the various cases. Due to the large amount of "legalese" in the documents, my model often had a hard time 
differentiating the cases, so I ended up with one large miscellaneous group (or topic).


First I scraped wikipedia to the name of all the justices.

Then I scraped the site caselaw.findlaw.com to get the link to each individual case on that site, then grabbed the 
text of the opinions from each link.

I pulled out the name of each case and divided them between justices --since each case often had multiple 
justices writing opinions: majority opinion, concurring opinions and dissenting opinions.


Then I used my seperated-by-justice documents to see which justices were similar and what topics (from the topic modeling) 
they wrote about each year.

I used tf-idf (Term frequency - inverse document frequency) to pull out relevant words relative to the overall corpus (all the documents).
I used non-negative matrix factorization to do topic modeling. NNMF is a form of unsupervised machine learning, which I used to find topics based on a 
documents contents. NNMF factors high-dimensional vectors into a low-dimensionality representation. In this case, I choose 20 as the 
number of topics I wanted it to find. 

Here is one example of a case that my model correctly identified as a 4th amendment case based on the keywords highlighted:

![case text](https://raw.githubusercontent.com/10brink/10brink.github.io/master/sc%20case%20text.png)

Here are the results of case topics over the years:

![topics graph](https://raw.githubusercontent.com/10brink/10brink.github.io/master/sctopics.png)

I found it interesting that there were several cases related to native americans in the 1960's but virtually none after that. 
