Today a colleague noticed that he was using a [IIFE][iife] where a simple object literal might do, and he asked my opinion.  The code looked something like this:

    var codeWords = (function() {
        var list = ['ABC', 'DEF', /* ... */ 'XYZ'];
        var found = function(test) {
            return list.indexOf(test) > -1;
        };
        
        return {
            list: list,
            found: found
        };
    }());
    
Presumably the usage would mostly be something like:

    var codeWords.found('MNO');
    
I didn't ask if there was a good reason to also expose the `list` property as well.  I'm assuming he has his reasons.

He had looked this over and realized that he'd just gotten into a habit of doing something perhaps not too useful.  He asked why he couldn't he just do:

    var codeWords = {
        list: ...
        found: ...
    };
    
First of all, I did have to point out that this would change the internal code a little bit.  He would need to add a `this` inside the `found` function.  But he certainly could do that:

    var codeWords = {
        list: ['ABC', 'DEF', /* ... */ 'XYZ'],
        found: function(test) {
            return this.list.indexOf(test) > -1;
        }
    }
    
And this would work exactly the same:

    var codeWords.found('MNO');

So the question is: are there good reasons not to change this to the simpler style?

----------

Yes, I'm asking **you**.

----------

Go ahead and think about it.  I'll wait.

----------

My answer is, I'm afraid, that it depends.  If he's absolutely certain that he will only use this as above, `codeWords.found(something)`, then he might as well use the simpler object literal.  But consider this:


    ['PQR', 'GHI', 'STU'].map(codeWords.found);
    
This should return something like

    [false, false, true]
    
And it would work in the original version.  But it wouldn't in the altered one.

    console.log(['PQR', 'GHI', 'STU'].map(codeWords.found));
    //=> TypeError: Cannot read property 'indexOf' of undefined

And why is that you ask?

----------

Yes, please, go ahead and ask.

----------

Well, I'm glad you asked.

What we pass to `map` is a function, plain and simple.  But the `found` function from the IIFE version carries around a bit of extra context: it's bound to a closure which also includes the `list` array.  The one from the objection literal version, though, doesn't have any way to access that array.  When we passed it into `map` via `...map(codewords.found)` we were simply using a reference to the function.  When we try to use it, `this` will be bound to something else that knows nothing of our `list` property.

We can get around this of course with `Function.prototype.bind`:

    ['PQR', 'GHI', 'STU'].map(codeWords.found.bind(codeWords));
    
But that means we are changing the API of our system in order to make it easier to implement, not only that, we are making the API more complex.

And that's a bad thing.

A really bad thing.

 
   
    
  [iife]: http://benalman.com/news/2010/11/immediately-invoked-function-expression/