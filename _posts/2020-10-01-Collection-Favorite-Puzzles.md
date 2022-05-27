---
layout: post
title: A collection of my favorite logical puzzles
published: true
categories: [puzzle]
description: A collection of my favorite logical puzzles
comments: true
fullview: false
---

I have heard a lot of logical puzzles over the years, and I have now compiled a list of my favorite ones. None of these puzzles have dumb answers, nor do they require a PhD in mathematics to solve. 
Most of them are probably well known, but I have rewritten them in my own words. If anyone has any input to more cool puzzle, then reach out!

## Blue Eyes

xkcd.com's infamous Blue Eyes puzzle is hard, mind bending and beautiful. I have written an article about this puzzle before, where I presented my version of the puzzle text, my explanation of the official solution and my alternative solution. Check it out [here](https://dsolstad.com/puzzle/2017/02/24/Riddles-in-the-Dark-Blue-Eyes.html).

## The King and his Servants

A king has 100 servants and wants to find out how smart they are. He says to them that they will be getting a hat that is randomly either red or blue placed on their head. They will then be standing in a line, facing the back of the head to the one in front of them. Starting from the servant in the back, the king will ask them which color they have on their hat. If they answer wrong, the servant would be shot. The amount of red and blue hats are random, and they cannot see their own hat color, but they can see every hat in front of them. They can also hear what the person behind them answered. They cannot communicate while standing there, other than answering "red" or "blue", but they can come up with a strategy beforehand to make as many survive as possible. How can they guarantee that 99 servants has 100% chance of surviving?

![Servants](/images/servants.png)

<details>
  <summary>Solution</summary>
    This video shows a good explanation of the solution: https://www.youtube.com/watch?v=3-avaCx4Czk
</details>

## Three Guys at the Hotel

Three men goes to the reception of a hotel to get a room. The clerk says it costs $30 and each men gives the clerk $10. The men goes up to the room, but now the clerk remembers that the room is for sale right now and costs only $25. He then sends $5 with the bellboy up to the room to give to the men. The bellboy thinks that he can't divide $5 on three men, so he keeps $2 for himself and give the men $1 each. 
  
So to sum this up: Each men has payed $9 each, because they got $1 back. That is $27. The bellboy has $2, which brings the total at $29. Where is the last $1?

<details>
  <summary>Solution</summary>
    This puzzle is a bit mind bending and confusing. I remember hearing it from my high school teacher, where he said that the answer was just the way numbers work. However, that is not the case at all. The trick on this puzzle is how the last statement is framed. Saying that the bellboy has $2 is not correct, because the men has already payed for those. What should be correct is to say that 3 x $27 - $2 = $25.
</details>

## The Blind Man with the Pills

A blind man has 4 pills in his pocket. 2 red and 2 blue. He must swallow 1 red and 1 blue pill to survive. If he swallows two of the same color, he dies. He is alone. No one can help him. The pills has the same texture and smell. Time isn't really an issue, but let's say he has 2 minutes to swallow the correct pills before he dies. How can he guarantee that he survives?

<details>
  <summary>Solution</summary>
    He picks up one pill at the time and breaks them in two. He then separates the two halves in different piles and consumes one pile. Since each pile contains 1/2 Red + 1/2 Red + 1/2 Blue + 1/2 Blue = 1 Red + 1 Blue, he survives.
</details>

## The Mountain Climber

A mountain climber is on top of a mountain which is 200 meters tall. 100 meters below him is a platform he can stand on. Both the top of the mountain and the platform has a hook he can tie a rope to. The climber has a knife and a rope that is 150 meters long. How can he get all the way down? Note that he cannot jump several meters down and the rope needed to tie knots is too insignificant to matter.

![Climber](/images/climber.png)

<details>
  <summary>Solution</summary>
  He cuts the rope so that he has one that is 100 meters and one of 50 meters. He then ties the 50 meter rope to the top hook and creates a loop of the 100 rope, which he ties the 50 meter rope to. The length of this joint rope is now 100 meters and he can get down to the platform. He can now untie or cut the 100 meter rope loop and drag in it until it comes off the 50 meter rope. He can now get to the bottom by tying the 100 meter rope to the hook at the platform and get safe down.

![Climber solution](/images/climber_sol.png)
</details>

## Variable Swapping

This is more of a programming puzzle, but doesn't really require much programming skills. The task is simple: Two variables (a and b) can have any single value, be it a digit or a character. Swap the values of these variables without using any existing functions, arrays or creating new variables. Also, a,b=b,a is not allowed, as you can do in Python. I have verified that it works in C/C++, PHP and Perl, so think in C-style languages.

```c
a = 'a';
b = 'b';
```

<details>
  <summary>Solution</summary>
  An example code can be found <a href="http://codepad.org/QWPwIPx3">here</a>. The ^ sign is the XOR operator, which will convert the character to binary in C-based languages. However, with digits instead of characters it will probably work in any language and you can use basic addition and subtraction as well.  
</details>

## The Brothers

You are on the way to the beautiful city Oslo and are at a crossroads, where the road splits in two. You don't know the way. However, there are two brothers living next to the road who knows. One of them always tells the truth and the other one always lies. You don't know who is who and you can ask one of them only one question. What can you ask to get in the right direction to Oslo?

<details>
  <summary>Solution</summary>
 You can simply ask one of them "What would your brother say was the way to Oslo?" and then go the other way of what was answered.
 If you asked the brother who always told the truth, then he would say the answer that the lying brother would give, which would be the wrong way. If you would ask the lying brother, he would lie about the correct answer the honest brother would give, and then give you the wrong path. 
</details>

## Squares

Imagine that the following lines are matchsticks. The task it to move two of them to form 4 equally big fully squares, with no spare parts and no partial squares.

![Squares](/images/squares.png)

<details>
  <summary>Solution</summary>

The solution is actually very simple, but people usually spend way to much time solving it.  
![Squares solution](/images/squares_sol.png)
</details>

## 5+5+5=550

Make the equation 5+5+5=550 valid by drawing one straight line anywhere. Note that it is not allowed to draw a line over the equal sign.

<details>
  <summary>Solution</summary>
  545+5=550
</details>

## The Man and the Window

A man has a house and on the wall there is a square windows that is 1 meter tall and 1 meter wide. However, he wants to double the size of the window to get more light in, but still want it to be 1 meter tall and one meter wide. How can he achieve this? Note that the window must be square and flat just like a traditional window. 

<details>
  <summary>Solution</summary>

 This is probably the weakest puzzle on the list, with a bit of an unsatisfying answer, but I still find it amusing.  
![Squares solution](/images/window_sol.png)
</details>
