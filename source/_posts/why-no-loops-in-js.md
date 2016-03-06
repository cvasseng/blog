title: Why you shouldn't use for-loops in JavaScript, and how to avoid them
author: chris
categories: 
- programming
- javascript
---

(this is a repost of a lost article - it's a couple of months old as of writing this)

*Using for-loops in JavaScript is kinda messy, especially when trying to go through the keys of an object, or if trying to create a closure within it that depends on the iterator.*

The cool thing though, is that you don’t really need to use for loops in JavaScript (with one exception), which leads to much cleaner code, and potentially less bugs.

Let’s look at some examples!

## Looping through arrays, and dealing with closures inside the loop body

    Consider this code:
    //Assume nodes is an array of DOM nodes
    //Don't do it this way!
    for (var i = 0; i < nodes.length; i++) {
      nodes[i].onclick = function () {
        alert('Hello world from node ' + i);  
      };
    }

Can you spot the bug? It’s a pretty obvious one - but may be less obvious if not familiar with how variables in closures are captured. The expected result for most people would probably be that the nodetriggered an alert when clicked, containing the string Hello world from node with the node’s index in the nodes list tacked on. But that’s not what happens.

Instead, what happens is that all the nodes trigger an alert with the length of the nodes array tacked on when clicked. Why is that? Well, when defining closures in JavaScript, all variables accessible in the same scope as the closure will be captured by that closure. “Captured” just means “will be made accessible from”.

This means that referring to i referres to the variable i outside the closure. The closure won’t execute until the user clicks the node it’s attached to, and when they do, the i variable will have the value it had when done iterating through the nodes - which is the length of the array.

To further illustrate:

    //Assume nodes is an array of DOM nodes
    //Don't do it this way!
    for (var i = 0; i < nodes.length; i++) {
      nodes[i].onclick = function () {
        alert('Hello world from node ' + i);  
      };
    }
    i = '1337';

Now clicking any of the DOM nodes will spawn an alert with the text Hello world from node 1337.

Also notice how you can reference i outside of the loop. This is because in JavaScript, all variables are lifted to the top of the current scope regardless of where they were defined.

So how can we fix this? One approach is to use a self-executing closure:
      
      //Assume nodes is an array of DOM nodes
      //Don't do it this way!
      for (var i = 0; i < nodes.length; i++) {
        nodes[i].onclick = (function (myIndex) {
          return function () {
            alert('Hello world from node ' + myIndex);  
          };
        })(i);
      }

That looks pretty nasty, huh?

This creates a function that executes immediately, with one argument - myIndex. It’s called with i as its argument, thus copying the current value of i into myIndex inside the function scope. It then returns the actual function we want to bind to - which now uses myIndex instead. This will work as intended, but it’s quite messy.

Cue “The Right Way”...
    
    //Assume nodes is an array of DOM nodes
    nodes.forEach(function (node, i) {
      node.onclick = function () {
        alert('Hello world from node ' + i;  
      };  
    });

That’s much simpler, isn’t it? We no longer need a variable to keep track of the iteration, we no longer have to access the node through the array, and most importantly - we now have proper scoping inside the loop.
forEach is an array function. It accepts two arguments - the first is a function, the second is the calling context. The this keyword will point to the calling context within the function supplied in the first argument. The supplied function supports two arguments also - the first is an element in the array, the second will contain the current iteration count.

This function is very useful regardless of what you’re doing in the loop body, mainly because:
  * The loop body has its own local scope
  * It’s more straight-forward
  * No need to use a variable to keep track of the current element

## Looping Through Object Keys

So what about Objects?

Consider this code:
    
    var obj = {
      "field1": "foo",
      "field2": "bar  
    };
        
    //Don't do it this way!
    for (var o in obj) {
      if (obj.hasOwnProperty(o)) {
        alert(obj[o]);  
      }  
    }

Writing that code gets tedious after a while, and has the same issues as the above example as far as scoping goes. Instead, we can use the Object.keys() prototype. This returns an array of keys owned by the supplied object. So now we can use forEach on it!

    var obj = {
      "field1": "foo",
      "field2": "bar  
    };
        
    Object.keys(obj).forEach(function (key) {
      alert(obj[key]);
    });

That looks much nicer to me at least, and comes with much less caveats.
Filtering arrays

Consider the following code to remove the foo entry from an array:

    var data = [
      'foo',
      'bar'  
    ];
        
    //Don't do it this way!
    var newData = [];
    for (var i = 0; i < data.length; i++) {
      if (data[i] !== 'foo') {
        newData.push(data[i]);  
      }      
    }

So how can we do away with the for loop here? You might think about doing something like this:

    var data = [
      'foo',
      'bar'  
    ];
       
    //Don't do it this way!
    var newData = [];
    data.forEach(function (item) {
      if (item !== 'foo') {
        newData.push(item);  
      }  
    });

Which would work, and it is a bit nicer, but there’s a better way still.
Arrays have a function called filter that allows us to remove elements from inside it and return a new array. If the closure returns true, the element will be included in the new array. If it returns false, it won’t:

    var data = [
      'foo',
      'bar'
    ];
       
    data = data.filter(function (item) {
      return item !== 'foo';  
    });

That’s much nicer, isn’t it? It’s clear, straight-forward, and doesn’t use an extra variable. This function is also very useful to see if something exists in the array:

    var data = [
      'foo',
      'bar'  
    ];
      
    if (
         data.filter(function (item) {
           return item === 'foo';
         }).length > 0
       ) {
        //There's a "foo" entry in the data!
    }

## When you only need a few elements..

In some cases, it’s not necessary to iterate through every element in an array. An example would be if you need to find a particular entry. Let’s say we have an array of objects, and want to call the hello function of the objects whose name is foo. But we don’t know where in the array it is:

    var data = [
      {
        'name': 'foo',
        'hello': function () {
          alert('hello from foo!'); 
        }  
      },
      {
        'name': 'bar',
        'hello': function () {
          alert('hello from bar!');  
        }  
      }
    ];
       
    //Do not do this!
    data.forEach(function (item) {
      if (item.name === 'foo') {
        item.hello();  
      }  
    });

This is bad. Why? Because it loops through all the elements in the array. It’s not possible to stop iteration of a foreach by returning either, since that will only return from the closure - not the loop.

Luckily, there’s a function for that, and it’s called some. some is another array function. It’s identical to forEach with one crucial exception: returning true from the loop callback will stop looping:

    var data = [
      {
        'name': 'foo',
        'hello': function () {
          alert('hello from foo!'); 
        }  
      },
      {
        'name': 'bar',
        'hello': function () {
          alert('hello from bar!');  
        }  
      }
    ];
       
    data.some(function (item) {
      if (item.name === 'foo') {
        item.hello();
        //Exit from the loop
        return true;  
      }  
    });

Mapping objects - when you want to turn one object into another
In some cases, it’s useful to be able to take an object and then either supplement its properties, or filter them in some way. For this, we have the map function:

    var data = [
      {
        "name": "foobar",
        "uselessAttribute": "asdlhasd"  
      },
      {
        "name": "helloworld"
      } 
    ];
        
    //Add an ID field to all the objects in the list, 
    //and remove the uselessAttribute field
    data = data.map(function (item, i) {
      return {
        "name": item.name,
        "id": i  
      };
    });
       
    //data now looks like this:
    [
      {
        "name": "foobar",
        "id": 0  
      },
      {
        "name": "helloworld",
        "id": 1
      }
    ]

The map function has some very interesting uses. One that I often use is in serialization. Let’s say we have a collection object of some sort, maintaining a list of other objects as well as internal properties. Let’s also assume that each of the contained objects have a function - toJSON - that returns an object with the properties it serializes. Now let’s say we want to serialize the collection object:

    function MySubObject {    
      function toJSON() {
        //...serialization code here...  
      }
       
      //Public interface
      return {
        toJSON: toJSON,
        ...
      }  
    }
          
    function MyObject {
      var children = [], //Children contains MySubObject objects
          properties = {
            //Lots of properties here  
          }
      ;    
         
      function toJSON() {
        return {
          properties: properties,
          //Notice how we serialize all the children in one go
          children: children.map(function (child) {
            return child.toJSON();  
          })  
        }  
      }
       
      return {
        toJSON: toJSON  
      }
    }

## The Exception

There’s one use case that for loops (or while-loop) are still valid for, and that is if you need to do something x amount of times, where x is just a number and not the length of an array.
    
    for (var i = 0; i < 99; i++) {
      console.log(i);
    }

I’m not aware of any way of doing this without using loop keywords. And even if there is one, it’s likely that using a regular for loop is better.
Or you can write your own boilerplate:

    function forRange(c, fn, ctx) {
      for (var i = 0; i < c; i++) {
        fn.apply(ctx, [i]);  
      }
    } 
      
    forRange(99, function (i) {
      console.log(i);  
    });

## Further reading

Also check out reduce and every!