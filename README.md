# rs_blog
thoughts and musings
## __2021-10-30__ ##
* got sick
* work is busy
* side projects (woodworking)

useful utility for splitting up an iterable into smaller lists - there are many way to do this but I settled on this one because it doesn't use any additional modules
```python
def splititer(aniter, maxlen):
    cuts = [x for x in range(0, len(aniter),maxlen)] + [len(aniter)]
    newlist = [aniter[cuts[i]:cuts[i+1]] for i in range(len(cuts)-1)]
    return newlist
```

## __2021-09-12__ ##
Reading through more of the electroncis books today.
- Adafruit circuit playground looks like fun - especially given python support
- FPGAs are a completely new concept to me - they say fast and affordable but Arduino and Raspberry Pi are cheaper - need to do more reading.
- breadboards look interesting but I need to know more about how to make functional circuits before I cand do anything with them

Also found that hugging face has a bunch of [datasets](https://huggingface.co/datasets ) that can be used for analysis practice. Putting it here so I don't forget.

Roman Orac posted an [article](https://towardsdatascience.com/7-free-ebooks-every-data-scientist-should-read-in-2020-32508ad704b7) on medium about free data sscience books online:
 - [Deep Learning](https://www.deeplearningbook.org/)
 - [Dive Into Deep Learning](https://d2l.ai/)
 - [Machine Learning Yearning](https://www.deeplearning.ai/machine-learning-yearning/)
 - [Interpretable Machine Learning](https://christophm.github.io/interpretable-ml-book/)
 - [Bayesion Methods for Hackers](https://github.com/CamDavidsonPilon/Probabilistic-Programming-and-Bayesian-Methods-for-Hackers)
 - [Python Data Science Handbook](https://jakevdp.github.io/PythonDataScienceHandbook/)
 - [An Introduction to Statistical Learning](http://faculty.marshall.usc.edu/gareth-james/ISL/)

Roman has similar articles for [Free AI courses](https://towardsdatascience.com/top-7-free-artificial-intelligence-courses-from-the-ivy-league-universities-7c951f787a55) and [Free programming books for data scientists](https://towardsdatascience.com/7-free-programming-books-every-data-scientist-should-read-in-2020-608c00d7cf3c)

## __2021-09-11__ ##
Most of my time lately has been devoted to handling my flooded basement so have been doing much data science.  I have been reading the twitters and these links look compelling.
- [255 data science projects](https://medium.com/coders-camp/130-python-projects-with-source-code-61f498591bb)
- [180 data science projects](https://medium.com/coders-camp/180-data-science-and-machine-learning-projects-with-python-6191bc7b9db9)
- [common data science interview questions](https://towardsdatascience.com/10-highly-probable-data-scientist-interview-questions-fd83f7414760)

I also got a cool bundle of electronics books from humblebundle and have been reading up on the adafruit circuit playground.  Unfortunately most of text is devoted to microsofts drag&drop no-code solution for the platform.  My interest is mainly around circuit python.

## __2021-08-21__ ##
I keep storing plaintext notes on my phone - I should just put them here so that I don't have to transcribe. Transcription gives me flashbacks of middle school, bruising my fingers learning to touch type random text in a page holder next to the manual typewriter.  Exercise for the reader: am I that old, or was my school just that far behind the times.

Some datasets I would like to work with:
- http://earthdata.nasa.gov - various NASA datasets about home
- https://www.judgeforyourselves.com/repository/ - at some point they'll publish all the documents on that family (you know the one)
- http://rawg.io/apidocs - because I can't find video games that I like of late
- https://cis.org/Immigration-Statistics-Data-Portal - took some time to find one that appeared to be 'independent'

## __2021-08-14__ ##
### Work Stuff
* large correlation analysis of CPU and volume metrics
* parsing ps log files for NLP type data
* pulling in data from JIRA for augmenting standard reporting

### Home stuff
* playing around with gui for Q&A for spanish training
* trying to make a decision about how to do the vocab piece
* trying to determine if it's feasible to host a pseudo-blog directly on github vs putting 'articles' on LinkedIn (other blog-like options)
  * do a blog as ipynb files with code samples
  * get a web site again

Read an interesting article on how p-value is not all that it's cracked up to be and just used to classify findings as 'statistically signficant'.  

I've been playing around with different ways of saving articles to read later as I find them on twitter.  Ideally this would integrate a text-to-speech solution so I could listen to the articles while I'm doing other things.

I have been struggling with context switches in the form of Markdown variations between tools.  I use github, JIRA, and Mattermost and they all have slight variations in syntax.  I think github and mattermost are relatively close, but have not researched it.  Since the posts in mattermost are usually pretty short there isn't a lot of call for extensive formatting.  JIRA is a completely different animal: lists and links are handled differently.
