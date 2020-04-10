---
layout: post
title: "Understanding software inconsistency with examples"
# image: /img/consistency.png
subtitle: 
# bigimg: /img/path.jpg
published: true
tags: [code review, best practices]
excerpt: As a software system is modified, its disorder, or entropy, tends to increase. Let's do a code review exercise to spot some inconsistencies in the following code snippet.
comments_id: 4
redirect_from:
 - /consistency-is-crucial
---

## Consistency is not so consistent

This is a two part dialogue regarding consistency in code. We will explore how and why a code becomes inconsistent and some techniques & best practices to spot and prevent these inconsistencies from happening.

Let's warm up and do a code review exercise to spot some inconsistencies, in the following code snippet.

{% highlight javascript linenos %}
// Models/Room.ts
import {IAppliance} from "../interfaces/Appliance.ts"
import {IDoor} from "../interfaces/door.ts"

interface Room {
    name: string
    addDoor(door: IDoor)
    deleteDoor(door: IDoor)
    putAppliance(appliance: IAppliance)
    removeAppliance(appliance: IAppliance)
}
class RoomImplementation {
    // 
}
{% endhighlight %}

Alright, now select from below the inconsistencies you spotted.

- <input type="checkbox"> Line 2/3 - **A**ppliance.ts & **d**oor.ts. Inconsistent file naming convention. Pascal case and Camel case.
- <input type="checkbox"> Line 5 - `interface Room` should be `interface IRoom`. The interfaces seem to be prefixed with **I** per line 3,4.
- <input type="checkbox"> Line 7/8 - Line 8 gives the impression that multiple doors are allowed in a room. Now `add` generally pairs with `remove` or `subtract`. The word `deleteDoor` goes against the grain and makes the code inconsistent.
- <input type="checkbox"> Line 7/9 - Similar to having multiple doors in a room, there could be multiple appliances. Using `add` in one case vs `put` in another makes the code lose its consistent meaning.
-  <input type="checkbox"> Line 8/10 - Similar to previous point using delete and remove interchangeably may not be a good idea and causes the code to become inconsistent.
-  <input type="checkbox"> Line 5-11 - Depending on how the code is currently organized, it looks like interfaces are grouped inside the interfaces folder whereas the Room interface lives outside in its own file. 

We just code reviewed 14 lines and more than **62%** have inconsistencies!

As you can guess, these can get subjective pretty quickly too, but its important not to lose sight & heart. The point is that in order to build a sustainable product every effort should be made to avoid inconsistencies in the code.

As Ivar Jacobson rightfully said:

> As a system is modified, its disorder, or entropy, tends to increase.

In the [next article](/importance-of-software-consistency-reasons-and-resolution) in this series, you can read about what makes consistency so desirable, reasons that software become inconsistent and how to remedy it.


Reference: 
- https://en.wikipedia.org/wiki/Software_entropy
