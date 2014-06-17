My [recent article][why-r] on functional composition in [Ramda][r] breezed over an important topic.  In order to do the sort of composition we would like with Ramda functions, we need these functions to be curried.

Curry?  Like the spicy food?  What?  Where?

Actually, `curry` is named for Haskell Curry, who was one of the first to investigate such techniques.  (Yes, they used his first name for a functional programming language too; not only that, but Curry's middle initial was 'B', which of course stands for [Brainf*ck][bf].)

Currying is the process of turning a function that expects multiple parameters into one that, when supplied fewer parameters, returns a new function that awaits the remaining ones.

The basics look like this:

    // uncurried version
    var formatName1 = function(first, middle, last) {
        return first + ' ' + middle + ' ' + last;
    };
    
    formatName1('John', 'Paul', 'Jones');
    //=> 'John Paul Jones' // Ah, but the musician or the admiral? :-)
    
    formatName1('John', 'Paul');
    //=> 'John Paul undefined');

But a curried version behaves more usefully:

    var formatNames2 = R.curry(function(first, middle, last) {
        return first + ' ' + middle + ' ' + last;
    });
    
    formatNames2('John', 'Paul', 'Jones');
    //=> 'John Paul Jones' // Definitely the musician!
    
    formatNames2('John', 'Paul');
    //=> returns a function
    

Here is a slightly more meaningful example.  If you want to find the sum of a collection of numbers, you could do this:

    // Plain JS:
    var add = function(a, b) {return a + b;};
    var numbers = [1, 2, 3, 4, 5];
    var addFirstFive = numbers.reduce(add, 0); //=> 15
    
And if you wanted to write a generic function that would total any list of numbers, you might write:

    var total = function(list) {
        return list.reduce(add, 0);
    };
    var sum = total(numbers); //=> 15
    
In Ramda, `total` and `sum` are very similar.  You can define `sum` like this:

    var sum = R.reduce(add, 0, numbers); //=> 15

But because `reduce` is a curried function, when you skip the last parameter, as in the definition of `total`:

    // In Ramda:
    var total = R.reduce(add, 0);
    
you simply get back a function you can call:

    var addFirstFive = total(numbers); //=> 15
    
Note again just how similar the definition of the function and the application of that function to data can be:

    var total = R.reduce(add, 0); //=> function:: [Number] -> Number
    var sum =   R.reduce(add, 0, numbers); //=> 15
    





(Some will insist that what we're doing is more properly called "partial application", and that "currying" should be reserved for the cases where the resulting functions take one parameter, each resolving to a separate new function until all the required parameters have been supplied.  They can please feel free to keep on insisting.)

  [why-r]: http://fr.umio.us/why-ramda/
  [r]: https://github.com/CrossEye/ramda
  [bf]: http://en.wikipedia.org/wiki/Brainfuck