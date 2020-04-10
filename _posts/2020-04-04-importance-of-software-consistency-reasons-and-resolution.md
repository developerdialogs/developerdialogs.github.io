---
layout: post
title: "Importance of software consistency - reasons and resolution"
# image: /img/consistency.png
subtitle: 
# bigimg: /img/path.jpg
published: true
tags: [productivity, best practices]
excerpt: Why is consistency in software so important ? What factors contribute to it and what we can do to resolve them.
comments_id: 8
---

*If you haven't already, check out the [previous article](/understanding-software-inconsistency-with-examples) in this series, to see an inconsistent code sample and practice a code review to see if you can spot all the inconsistencies.*

---- 

Code is harder to read than to write and a consistent code is 100 if not a thousand times easier to read. While reading code, a programmer needs to visualize the the algorithm, memorize several indirections and a simple oversight (noticed the double "the" in the previous sentence ?) can cause bugs in the code.

Not only this, a consistent code will help new members on the team to become productive quickly. This is because its easier to teach them one coding style that your team follows, instead of having them learn myriad of different practices present in your codebase.

Lastly, if developers have a consistent foundational knowledge of their practices and conventions, it makes it a lot easier for them to communicate and share knowledge amongst themselves.

Understand, that the number one method of bringing down your engineering cost is by increasing the productivity of your team.


Next, let's take a look at some of the reasons these inconsistencies may get introduced:
 
1. **Learning stage** - I can barely understand the code I myself wrote a year ago. A programmer organically goes through different stages of learning and depending on where they are at, the code style between a span of months can change drastically.

    *Resolution*: This is normal and do not lose sight of your learning. When working on a personal project or for your curriculum, research and apply best practices, while trying to keep things consistent. Understand that this is a part of the learning curve. Make conscious effort to improve and put effort in maintaining your code.

1. **Under Communication** - Lack of Communication is perhaps the most important factor that contributes to inconsistencies in a codebase. Silos between programmers can wreck havoc on codebase even if everyone have good intentions and are constantly working to improve the code.

    *Resolution*:  **Communicate!** If you identify certain issues, bring it up with your colleagues. Have a chat with your leads and document the outcomes. This would also be a great time to make use of any ticketing system that you use and document the inconsistencies or imprvements in a backlog ticket. 

    Make sure to discuss and prioritize tasks with your teammates befre starting working on it. After all, it is the responsibility of a team to deliver their best work together.

    Code reviews - Lastly, code review is an important piece which ensures that the collective knowledge of team is applied. Be constructive in giving feedback and be gracious when receiving it.

1. **Team size** - People are opinionated and have different learning experiences. As the team size increases, so will the variations that each programmer in the team tend to introduce. 

    *Resolution*: Experienced professionals will make conscious effort in identifying & understanding the project's current coding coventions and build upon them. Chat with your teammates, to find out if there is a knowledge source available. 
    
    If not, dive into the code and pair program with them for a couple hours or even days if needed. Most importantly, document your findings and share it with the team. It is your responsibility to make onboarding easier for the next person who joins your team.

    As the team and organization size increases, it becomes important to automate as much stuff as possible. Invest time into exploring tools that can force a consistent coding style and conventions for your developers through hooks, code reviews etc.

1. **Time/History** - Software Development has been going on for more than half a century and you will often be working in existing software systems. 5, 10 even 20 year old code in some cases. This software has gone through an evolution cycle of its own. If active efforts were not made to define the conventions to follow, this legacy code may have tens, hundreds or even thousands of variations introduced slowly over the course of years.

    *Resolution*: Think of code as a complex living organism. It has evolved over the years while continuing to grow. How would you diagnose and treat such an organism ? 

    First and foremost, identify if there is even a disorder ? Things that seem inconsistent to you, are they actual disorders or just features. After all, different length fingers in a human body is part of the evolution!
    
    After diagnosis, one possible answer would be to measure, test and repair one disorder at a time. This would translate to:

    1. document the inconsistencies
    1. write tests to make sure your changes work as expected and
    1. fix the inconsistencies.

    Another possible answer, depending on how bad the inconsistency or disorder is, to replace the defected piece. Be careful though, this should probably be your last resort! And make sure you do not fall into [this trap.](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/){:target="_blank"}

1. **3rd party dependencies** - This might not seem obvious at first, but let me try explaining it. Imagine yourself using a 3rd party library that in the world of Promises & Futures, still uses callbacks. Now this library is essential and because you do not have time to waste, you end up sacrificing consistency in lieu of using the library. This however will result in your code having a mix of promises and callbacks and now, you will have to add weird promise constructors every time you use this library.

    *Resolution*: An easy fix for this problem is following good coding principles. Did you consider using adapter pattern for the library to match your coding practices? If not, see if you can identify the reason why this was the case.

Software Consistency is a wide topic and conscious steps are necessary in order to maintain it in the long run. And don't forget, this is just one of the several items required in order to build a sustainable product, but it sure is an important one!
