---
layout: post
title: "OO Recursive Search"
date: 2013-12-17 08:55
comments: true
categories: 
---

This is a further exploration on the topic of OO Recursion.  In the last post, we looked at seeding Mancala wells.  In this post we'll look at searching for an object in an OO Recursive fashion.  

To start things off, here is our simple Node class;

```ruby Simple Node class
class Node

  attr_accessor :name

  def initialize(name)
  	@name = name
  	@children = Array.new
  end

  def add_child(child)
  	@children << child
  end

  def find_node_named(name)

  	if @name == name
  		return self
  	end

  	@children.each do |child|
  		found = child.find_node_named(name)
  		if(found != nil)
  			return found
  		end
  	end
  	nil
  end
end
```

Our Node class has a name and keeps a collection of its children.  There is the add_child method that adds a child to its collection of children.  

The find_node_named method will be of most interest to us.

``` ruby 
def find_node_named(name)

  if @name == name
 	return self
  end

  @children.each do |child|
  	found = child.find_node_named(name)
  	if(found != nil)
 		return found
  	end
  end
  nil
end
```
This method has one parameter, name,  that is used to look for the node of interest.

The first thing that is done is checking to see if this node is the one being searched for.
```ruby
if @name == name
  return self
end
```
This is the base case. If the parameter name and the node's name match, then self is returned and the search is complete. The object knows about itself and can determine if it is the object being searched for or not.

If this is not the object we are looking for then the search continues on the objects children.
```ruby
@children.each do |child|
  found = child.find_node_named(name)
  if(found != nil)
  	return found
  end
end
```

The nodes children are iterated over, sending the find_node_named to each one till it is found or there are no more children. If the node is found then the found node is immediately returned to the caller.

The last line in the find_node_named method returns nil if the node is not found.  When the the inidtial test of name against the current node's @name identifier do not match, the method will then iterate over the objects children.  If the iteration over the children does not find the node identified as the parameter name, then nil is returned.

Pretty straight forward.  

Next lets add a leaf node.

```ruby LeafNode
class LeafNode

  attr_accessor :id

  def initialize(name)
  	@id = name
  end

  def add_child(child)
  	#do nothing
  end

  def find_node_named(name)
  	if @id == name
  		return self
  	else
  		nil
  	end
  end
end
```

The LeafNode provides the same api's as the Node. Though as a leaf node, it will not have children  and the add_child method will do nothing, a no-op. 

Just to make it more interesting,  instead of an instvar named @name to identify the node,  the instvar for the identifier has been changed to @id.  

Based upon the way we wrote our original find_node_named method in Node,  we won't have to handle the case of a LeafNode's identifier @id when searching through the Node's collection of children.  

The LeafNode demonstrates polymorphic behavior of the Node for the search.  A different object (LeafNode) has been thrown into the data structure and for the purpose of the search provides similar behavior as Node.  Since the Node and LeafNode know how to test themselves and search their children, in the case of Node, then the addition of the LeafNode doesn't disrupt the original Node 
find_node_named method to handle LeafNode.  

OO recursion can make search complex structures much simpler when taking advantage of the power of the object and data encapsulation.  Let the objects do the work. 

Here's a link to the example provided; <a href=https://gist.github.com/thutch/8084791>OO Recursive Search gist</a>

