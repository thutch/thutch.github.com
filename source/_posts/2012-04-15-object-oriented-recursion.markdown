---
layout: post
title: "Object-Oriented Recursion"
date: 2012-04-15 11:16
comments: true
categories: [OO, Recursion, Polymorphism]
publish: true
---

To understand recursion,  you must first understand recursion... 

Google "recursion" and the top result will be "Did you mean: [_recursion_](https://www.google.com/search?source=ig&hl=en&rlz=&q=recursion&oq=recursion&aq=f&aqi=n1g10&aql=&gs_nf=1&gs_l=igoogle.3..5j0l10.597.1713.0.2449.7.7.0.0.0.0.63.375.7.7.0.)"

In college one of my professors had stated that recursion is a method for making the computer do most of the work for you.  Its a way to reduce a a complex algorthm to a few lines with a base case.  
   
But object-oriented recursion is a bit different;
<!-- more -->

{% blockquote Kent Beck, The Smalltalk Report http://www.macqueen.us/smalltalkReport/ST/ST09/27be.pdf Object-Oriented Recursion %}
object-oriented recursion represents the invocations themselves as objects, sending the same message to different objects
{% endblockquote %}


{% pullquote %}
Procedural recursion is coded as a function that calls itself with different parameters until a base case is met.  
Let's have {"a quick review of procedural recursion"} using a purposely verbose factorial function;
{% endpullquote %}
```ruby A puposely verbose factorial function
def factorial aNumber
	if (aNumber = 1)
	  result = 1
	else
	  recursiveCallResult = (factorial(aNumber - 1))
	  result = aNumber * recursiveCallResult 
	end

	return result
end
```  
Okay, so we need to know the factorial of 3. We have written the above function and now we'll execute it with the number three. The execution would look something like this (the verbosity will make sense)


    #factorial(3)  
    3 != 1  
    recursiveCallResult = (factorial(3 - 1))  
    result = null  

    #factorial(2)  
    2 != 1  
    recursiveCallResult = (factorial(2-1))  
    result = null  
   
    #factorial(1)  
    1 == 1  
    recursiveCallResult = null  
    result = 1  

  The base case of the function is met and the operation begins to "unwind" back up to the original call;

    #factorial(2)    
    recursiveCallResult = 1  
    result = 2 * 1  
    result = 2   

    #factorial(3)
    recursiveCallResult = 2  
    result = 3 * 2  
    reusult = 6  

As it turns out, the factorial of 3 is 6!! Cool!

{%pullquote%}
{"Object-Oriented Recursion"} is a way to make the objects do all the work!!
{%endpullquote%}  

Kent Beck's article on <a href=http://www.macqueen.us/smalltalkReport/ST/ST09/27be.pdf>Object-Oriented Recursion</a> was an epiphany to me when I first read it in 1996.  It opened my eyes to different ways of working with objects.  It made even more clear to me the concept of <a href=http://www.wordiq.com/definition/Polymorphism_in_object-oriented_programming>polymorphism</a>.  To be able to send the same message to different objects is a powerful concept.  

 I have used the Peano Number example from the article when teaching myself a new programming language as it helps me to understand how polymorphism can be applied in that language.  Also the example is just plain fun.  If interested, the translations are available for <a href=https://github.com/thutch/PeanoNumber.Ruby>Ruby</a>, <a href=https://github.com/thutch/PeanoNumber.CSharp>C#</a>, and <a href=https://github.com/thutch/PeanoNumber.DartLang>Dart</a>.

#### Mancala & OO Recursion

Recently I participated in a <a href=http://dallashackclub.com/>Dallas Hack Club</a> where the kata for that night was the <a href=https://gist.github.com/2153731>Mancala game</a>.  Mancala is a two player game where each person takes turns "sowing" seeds from well to well.  

One of the classes that we created for the Mancala game kata was the Well.  A Mancala 'well' holds a number of seeds and is owned by a player.  On a players turn, the player will take all the seeds from a selected well,  then "sow" a single seed to each well starting on the well to the right till all the seeds picked up from the initial well have been "sown", then the next player takes their turn.  

{%pullquote%}
The 'Well' class for the Mancala game presented an excellent opportunity to {"demonstrate an object-oriented recursion solution."} Especially since there is also a GoalWell which is handled a bit differently when the seeds are sown.  A player can only place a seed in a goal well when the player owns that goal well.  
{% endpullquote %}

```ruby  Class Well
class Well
  attr_accessor :next_well, :seeds, :owner

  def initialize anOwner
    self.seeds = 0
    self.owner = anOwner
  end
  
  def sow
    current_seeds = self.seeds
    self.seeds = 0
    self.next_well.take_seed(current_seeds, self.owner)
  end

  def take_seed(count, player)
      self.seeds += 1
      count = count - 1
      if count > 0
       self.next_well.take_seed(count, player)
    end
  end
end 
```

Each well knows about the well to its right, its next_well, so the data structure is a circular linked list.  
``` ruby
player1 = :player1
well01 = Well.new(player1)
well02 = Well.new(player1)

well01.next_well = well02
well02.next_well = well01
```

Each well can be assigned a number of seeds; 
`well01.seeds = 2`

When gameplay begins,  a player will select a well and will "sow" from that well; `well01.sow`

The sow method is the starting method for the operation.
```ruby  Well class sow method
  def sow
    current_seeds = self.seeds
    self.seeds = 0
    self.next_well.take_seed(current_seeds, self.owner)
  end
```

1) In this method, the current number of the seeds is held in a temporary variable "current_seeds"; `current_seeds = self.seeds`  
2) The seeds instVar is set to 0; `self.seeds = 0`  
3) Lastly, the well's next_well is requested to take_seed sending the current_seeds and starting well's owner for the parameters.
This line of code `self.next_well.take_seed(current_seeds, self.owner)` begins the object-oriented recursive operation,  

Lets have a closer look at the `take_seed` method;
```ruby  Well class take_seed method
def take_seed(count, player)
   self.seeds += 1
   count = count - 1
   if count > 0
     self.next_well.take_seed(count, player)
    end
end
```
1) One is added to this well's seed count; `self.seeds += 1`.  
2) The count that is passed in is reduced by 1; `count = count - 1`  
3) `if count > 0` tests for the base case. If count is **not** greater than zero, then the base case is met and the operation will not continue.
3) If count is greater than zero, then the next_well is called to `take_seed'

{%pullquote%}
Lets now look at the {"Mancala Goal Well"}. According to the rules a player can only place seeds in his own goal well.  
Lets create a subclass of Well to handle this special case for take_seed.
{%endpullquote%}
```ruby GoalWell
class GoalWell < Well
  def take_seed(count, player)
    if player == self.owner
      super
    else
      self.next_well.take_seed(count, player)
    end
  end
end
```

When a GoalWell is sent the message take_seed, it must first check if the player is the same as the owner.  If it is, then it will call its super class' implementation of the take_seed method.

If the GoalWell's owner is not the same as the player,  then the GoalWell call's its next_well to take_seed.  The distribution of the starting well's seeds continues till the base case of no more seeds (a count of zero) is reached.  Then the next player can select a well and sow.  

Okay, we've described the code,  lets look at what the "sow" execution would look like.

{% pullquote %}
Let's create some wells and do {"a step by step description of the execution"}.
{% endpullquote %}

    well01 = Well.new(:player1)  
    player1GoalWell = GoalWell.new(:player1)  
    well02 = Well.new(:player2)  
    player2GoalWell = GoalWell.new(:player2)`

Now lets link our wells up together;
    well01.next_well = player1GoalWell  
    player1GoalWell.next_well = well02  
    well02.next_well = player2GoalWell  
	player2GoalWell.next_well = well01

We'll place 3 seeds in well01, `well01.seeds = 3`.  The other wells seeds are initalized to 0.  Play will begin with with well01 invoking "sow".   

    #well01.sow
      ------instVars well01 -----
      well owner is player1
      self.seeds is 3
      next_well is player1GoalWell
      ------------------------------
      current_seeds is 3
      self.seeds is set to 0
      next_well(count=3, player=:player1)
    
player1GoalWell is the next_well and belongs to player1.  Also player1GoalWell is a GoalWell class.

    #player1GoalWell.take_seed(count = 3, player = :player1)
      ------instVars for player1GoalWell -----
      owner is :player1
      self.seeds is 0
      next_well is well02
      ---------------------------------
      :player1 does equal self.owner <:player1>
        so we call the super take_seed implementation on the Well class
      self.seeds = 1 = 0 + 1
      count is 2 = 3 -1
      count is GREATER than 0 and the base case has NOT been met, execution continues
         next_well(count=2, player=:player1)

well02 is the next_well and belongs to :player2.  It is a Well class.

	#well02.take_seed(count = 2, player = :player1)
      ------instVars for well02 -----
      owner is :player2
      self.seeds is 0
      next_well is player2GoalWell
      ---------------------------------
      self.seeds = 1 = 0 + 1
      count is 1 = 2 -1
      count is GREATER than 0 and the base case has NOT been met, execution continues
         next_well(count=1, player=:player1)

The next_well is player2GoalWell. It belongs to :player2, so it will do something a bit different;

    #player2GoalWell.take_seed(count = 1, player = :player1)
      ------instVars for player1GoalWell -----
      owner is :player2
      self.seeds is 0
      next_well is well02
      ---------------------------------
      :player1 does NOT equal self.owner <:player2>
        so the code calls the next_well  take_seed implementation on the Well class
      next_well(count=1, player=:player1)
      self.seeds remains 0

The next_well is well01, right back where we started from!

	#well01.take_seed(count = 1, player = :player1)
      ------instVars for well01 -----
      owner is :player1
      self.seeds is 0
      next_well is player1GoalWell
      ---------------------------------
      self.seeds = 1 = 0 + 1
      count is 0 = 1 - 1
      count is NOT greater than 0.  The base case has been met so the operation stops here!!

Each of the wells count for seeds should be  
		well01.seeds is 1  
  		player1GoalWell is 1  
  		well02.seeds is 1  
  		player2GoalWell is 0  


{% pullquote %}
This Mancala Well example demonstrates Object-Oriented Recursion with two well types, Well and GoalWell being polymorphic, performing the take_seed operation. Each well delegates to the next well to handle updating their seed count, decrementing from the seeds being sown and testing for the base case to end the sow operation.

 {"Have fun with OO recursion!!"}
{% endpullquote %}


The source for this example can be found at <a href=https://gist.github.com/2395565>Mancala Well</a>

