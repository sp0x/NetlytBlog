---
layout: post
title:  "To Quote or Not To Quote? That is the question!"
date:   2018-09-05 18:27:42 +0200
categories: case-studies
---


As everyone who uses social media you've probably noticed the rise of inspirational quotes. Be it posts on a sunny background from a friend you rarely hear from. A wise piece of thought supposedly with a picture of Einstein himself! We've all seen them.
Most of the time they annoy us, but on rare occasions just the right quote appears and we feel compelled to share it. This is normal behavior on social media. You see something you like you share it to gain a little bit of fame from finding that one thing no one else knew about. But have you ever thought of what makes a quote good? Why do people like one quote over the other?
To find the answer we went to Reddit and collected some 490,000 quotes spanning the last 10 years.
If we look at the distribution of karma(Reddit's popularity score) for these quotes, we see that not all quotes are created equal. 

![](/assets/quote_karma.JPG)

Most quotes end up having almost 0 karma. Where as a few fortunate (19.72%) of posts account for 82.35% of the karma. With the top 5% accounting for 46% of the karma. Almost textbook Pareto distribution. So far nothing new. What makes these quotes so special? 
To answer this question we're going to use machine learning and NLP(Natural Language Processing) to analyze the text of each quote to try and find some patterns. 

And here's what we'll look into

1. The Age of the post (does karma increase with age?)
2. The Author (Are there any authors whose names make you more popular?)
3. Word Count (Should your quote be a single sentence or longer?)
4. Readability (Does the complexity of the language used in the text matter? Do I need to put my quotes in layman terms or should i try to look smart?)
5. Sentiment (Should my quotes be positive and inspirational! Or sad and depressing)
6. Topics (What topic gives me an edge over the other quotes? Politics? Love? Life lessons?)

<h2>Age</h2>

Does the age of the post affect its score? If the post is old then shouldn't it have a higher score because more people had the chance to see it? But we know that viral videos can spring out of nowhere gaining astronomical amounts of popularity in a few short days. So are our lucky quotes viral? Or do they gain their popularity with age?

![](/assets/quote_age.JPG)

As we can see most of the top 5% of quotes (thats the high scoring quotes) are relatively new (925 days old) compared to the bottom 95% (1033 days old). With a difference of more than 100 days we can say that age doesn't really help you improve your score. But with an average age of over 2 years it doesn't seem like these quotes can be called viral either. Maybe its not how long ago you posted your quote but when you posted? If we look at the number of posts each day for the 2 categories we see something very interesting.

![](/assets/daily_quotes.JPG)

The low performing quotes have a relatively stable pattern (the activity is higher during working days and lower during weekends).
Where as the activity chart for the high performers shows that they were release at the end and middle of the week. Posting at the end or middle of the week gives you a slight advantage increasing your score with around 5%.


<h2>Author</h2>

Common sense would suggest that the if a smart person says something then it must be true or at least not wrong. After all if a person is smarter or more famous than us then they probably know more about the world than we do. So it would make sense for us to look for good quotes from famous people. But will they're fame help us gain more likes?

<table>
  <tr>
  <th>Quote</th>
  <th>Author</th>
  <th>Score</th>
  </tr>
  <tr>
    <td>"I accept that people are going to call me awful things every day, and I will always defend their right to do so."</td>
    <td>Barack Obama</td>
    <td>11885</td>
  </tr>
  <tr>
    <td>"To announce that there must be no criticism of the President, or that we are to stand by the President, right or wrong, is not only unpatriotic and servile, but is morally treasonable to the American public. Nothing but the truth should be spoken about him or any one else."</td>
    <td>Theodore Roosevelt</td>
    <td>7075</td>
  </tr>
    <tr>
    <td>"We live in a country where if you want to go bomb somebody, there’s remarkably little discussion about how much it might cost. But when you have a discussion about whether or not we can assist people who are suffering, then suddenly we become very cost-conscious."</td>
    <td>Prof. Andrew Bacevich</td>
    <td>4992.0</td>
  </tr>
  <tr>
    <td>"The most fucked up joke life can play on you is letting you meet the right person at the wrong time."</td>
    <td>Unknown</td>
    <td>3905</td>
  </tr>
    <tr>
    <td>"I support anyone's right to be who they want to be. My question is: to what extent do I have to participate in your self-image?"</td>
    <td>Dave Chappelle</td>
    <td>3900</td>
  </tr>
  <tr>
    <td>"The child who is not embraced by the village will burn it down to feel its warmth"</td>
    <td>African proverb</td>
    <td>3547</td>
  </tr>
    <tr>
    <td>"We live in a country where if you want to go bomb somebody, there’s remarkably little discussion about how much it might cost. But when you have a discussion about whether or not we can assist people who are suffering, then suddenly we become very cost-conscious."</td>
    <td>Prof. Andrew Bacevich</td>
    <td>3281</td>
  </tr>
  <tr>
    <td>"Uber, the world’s largest taxi company, owns no vehicles. Facebook, the world’s most popular media owner, creates no content. And Airbnb, the world’s largest accommodation provider, owns no real estate. Something interesting is happening."</td>
    <td>Tom Goodwin</td>
    <td>2983</td>
  </tr>
    <tr>
    <td>"There are essentially only two drugs that Western civilization tolerates: Caffeine from Monday to Friday to energize you enough to make you a productive member of society, and alcohol from Friday to Monday to keep you too stupid to figure out the prison that you are living in."</td>
    <td>Bill Hicks</td>
    <td>2925</td>
  </tr>
  <tr>
    <td>"Socialism never took root in America because the poor see themselves not as an exploited proletariat, but as temporarily embarrassed millionaires."</td>
    <td>John Steinbeck</td>
    <td>2656</td>
  </tr>
</table>

The table above shows the 10 quotes with the highest score. As we can see almost all of them have their author specified but does that hold true for all good quotes?

![](/assets/top5_quotes_karma.png)

If we look at the karma distribution in the top 5% of quotes we see something very interesting. 38% of the karma is collected by unknown authors (quotes that don't specify the name of the author). 

<h2>Word Count</h2>

Word count: Should you keep your quotes short or should they be haiku styled essays expressing the depth of your soul. 

![](/assets/quote_len.JPG)

According to the data most of the high scoring quotes are also longer by 4.2 words on average. So longer quotes = higher scores?
Not quite. We need to know if the karma scores increase as you add more words and to do that we trained a small machine learning model. According to our model each word you add to your quote will increase your karma by just 0.45 points. So 10 words = 4.5 points right? Well yes in theory, but remember longer quotes impact readability. Which we'll explore next. 


<h2>Readability</h2>

What is readability? I hear you ask. Well simply put readability tells us how easy it is for the reader to understand the contents of the text. To keep things simple we'll use the [Dale-Chall readability score](https://en.wikipedia.org/wiki/Dale%E2%80%93Chall_readability_formula). Whats good about this formula is that it gives us a score corresponding to a US school grade. For example a score of 4 means that an average 4th grader can understand the text.

![](/assets/quote_readability.JPG)

Looking at the 2 graphs reveals that the top quotes have a slightly lower readability score (6.87 versus 7.04 for the bottom 95 %) making them easier to understand. This is also confirmed by our model which predicts that an increase of 1 level would result in a loss of 0.5 points of karma. Passionate writers be warned! Increasing the length of your quote might backfire on you if the text becomes too hard to understand.

<h2>Sentiment</h2>

Should your quotes be filled with hope? Or dark and gloomy? That's what our sentiment analysis will determine. But how does sentiment work? Well to put it simple we split all words in 2 categories - positive and negative. Positive words are those associated with ... well positive feelings such as joy, kindness, inspiration and so on. Negative are all words associated with bad emotions - anger, sadness and loneliness. Then we give each quote a score between -1 and 1, where -1 means the quote is highly negative, 1 that its positive and 0 means neutral. So which feelings are most common in our data? Well lets take a look!

![](/assets/quote_sentiment.JPG)

What does the graph say? Well the data seems to suggest that positive quotes are getting higher scores. So my quote should be inspirational right? As usual that's not the whole picture. If we take a look at the summary that pandas provides we see a completely different picture

{% highlight python %}
top5.sentiment.describe()
#=> count    2471.000000
#=> mean        0.028950
#=> std         0.508209
#=> min        -0.993300
#=> 25%        -0.400800
#=> 50%         0.000000
#=> 75%         0.458800
#=> max         0.987900

{% endhighlight %}

{% highlight python %}
bottom95.sentiment.describe()
#=> count    73007.000000
#=> mean         0.090947
#=> std          0.478631
#=> min         -0.988000
#=> 25%         -0.249800
#=> 50%          0.000000
#=> 75%          0.476700
#=> max          0.993000
{% endhighlight %}

As we can see the reality is that our top 5% is slightly more negative than the rest. And our model confirms this giving you -3.13 karma for every positive point. 

<h2>Topics</h2>

And finally topics! What should you talk about in your quotes? Well lets take a look at the most common terms found in the top quotes.

![](/assets/quotes_most_common_top5_terms.JPG)

We've managed to organize these words into 5 topics 

![](/assets/quote-topics.png)

Taking a closer look at the topics we can see that they're mostly relevant to teenagers. This is also supported by our findings in the readability tests. Where the scores seemed to suggest that a text suitable for 14-15 year olds would maximize your score.

<h2>What your quote should look like</h2>

So what makes a good quote? From what we've seen in the data we came up with the following steps that you should follow in order to get the best result possible:

- Be original as opposed to re-posting other people's quotes (unless they're former presidents)
- Keep the word count between 25 and 36 words
- Talk about topics that teens are interested in (love, relationships, growing up..etc)
- Make sure your average 14 year old can understand what your quote is about
- Be slightly gloomy (but don't be too depressing), show that there's light at the end of the dark tunnel
- Your post should spark a conversation (our model predicts that each comment you get on your post increases your karma by 11.8 points!)
