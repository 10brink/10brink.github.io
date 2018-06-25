---
layout: post
title: Creating a rating system for International Soccer teams
published: true
---

With the world cup coming up, I wanted to create a better system for rating international teams than the official FIFA rankings. 
Elo ratings are a good place to start. Elo ratings were originally created for chess but are used in a lot of competitive games, including esports.

Elo ratings are pretty straightfoward. Each team starts with a arbitrary number, usually 1500, and it is constantly updated after every game. 
Elo also gives the expected probability of an outcome. 
The formula looks like this:
Ea is the expected win rate:
![expected rate of winning](https://raw.githubusercontent.com/10brink/10brink.github.io/master/eloeq.png)

R'a is the updated rating after a match.
![change in rating after game](https://raw.githubusercontent.com/10brink/10brink.github.io/master/eloeq1.png)


where:
E = expected rate of winning
R = Elo rating 
K = Learning rate
S = Score, win = 1, draw 0.5 lose = 0


I used five years of intertional soccer matches, not counting friendlies, to get the ratings. I iterated through those matches
to find the optimal learning rate and home field advantage. Home field advantage is a number that is simply added to the elo score
of the home team before calculating the expected outcome and updating the rating. 
I optimized them by finding the lowest mean squared error, however, I should have used log-loss instead as this is a categorical problem (with three categories: win lose or draw)

![HFA](https://raw.githubusercontent.com/10brink/10brink.github.io/master/hfa.png)

![Learning rate](https://raw.githubusercontent.com/10brink/10brink.github.io/master/hfa.png)

This is the simplest application of elo ratings. I could possible increase the accuracy by accounting for number of goals scored/goal differential. 


Here are some of the ratings that I got:

|   Team    | elo Rating| 
| ------|:------:|
| Germany | 2058    |  
| Brazil | 2018 | 
| Spain | 2007  |
| Argentina | 2002| 
| Portugal | 1952 | 
| France | 1897|
| Mexico | 1896|
